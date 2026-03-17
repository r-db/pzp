# Webflow PZP Implementation

> Add PZP to your Webflow site. Visual design meets machine-native access.

## Overview

Webflow provides enough flexibility for PZP implementation through:
- Custom code injection
- Static page hosting
- API access to CMS content
- Custom domain configuration

**Approaches:**
1. **Path-based:** Host PZP files on Webflow (limited)
2. **Subdomain:** Separate PZP site synced with Webflow CMS (recommended)

---

## Quick Start: Path-Based Approach

### 1. Create PZP Pages

In Webflow Designer:

1. Create folder page: `/pzp`
2. Create pages:
   - `/pzp` (index)
   - `/pzp/context`
   - `/pzp/context/essential`

### 2. Configure Page Settings

For each PZP page:
- Disable Webflow branding
- Add custom code in page settings

### 3. Add robots.txt Content

In **Project Settings → Custom Code → Head Code**:
```html
<script>
// PZP Discovery - Add to robots.txt manually
// X-PZP-Site: true
// X-PZP-Manifest: /pzp/manifest.json
// X-PZP-Version: 1.0.0
</script>
```

Note: Webflow doesn't allow custom robots.txt. Use subdomain approach for proper discovery.

### 4. Create Manifest Page

Create `/pzp/manifest` page with embed block containing:
```html
<script>
document.write(JSON.stringify({
  "$schema": "https://pzp.riscent.com/schemas/manifest.schema.json",
  "version": "1.0.0",
  "protocolVersion": "1.0.0",
  "name": "Your Site Name",
  "description": "Your site description",
  "type": "content",
  "updated": new Date().toISOString(),
  "tokenBudgets": {
    "essential": 400,
    "full": 2000
  },
  "endpoints": {
    "context": "/pzp/context/",
    "actions": "/pzp/actions/"
  },
  "capabilities": ["read"]
}, null, 2));
</script>
```

**Limitation:** This outputs HTML, not JSON. Use subdomain approach for proper implementation.

---

## Recommended: Subdomain Approach

### Architecture

```
yoursite.webflow.io          → Webflow site (human)
pzp.yoursite.com             → Vercel/Netlify (PZP)
                             ↑
                        Webflow CMS API
```

### 1. Create PZP Project

```bash
mkdir pzp-webflow && cd pzp-webflow
npm init -y
npm install express cors node-fetch
```

### 2. Server with Webflow CMS Sync

**server.js:**
```javascript
const express = require('express');
const cors = require('cors');
const fetch = require('node-fetch');

const app = express();

// Configuration
const WEBFLOW_TOKEN = process.env.WEBFLOW_TOKEN;
const WEBFLOW_SITE_ID = process.env.WEBFLOW_SITE_ID;
const SITE_NAME = process.env.SITE_NAME || 'Your Site';

// Middleware
app.use(cors());
app.use(express.json());

// PZP Headers
app.use((req, res, next) => {
  res.set({
    'X-PZP-Site': 'true',
    'X-PZP-Version': '1.0.0',
    'Access-Control-Allow-Origin': '*'
  });
  next();
});

// Static files
app.use(express.static('public', {
  setHeaders: (res, path) => {
    if (path.endsWith('.md')) {
      res.set('Content-Type', 'text/markdown; charset=utf-8');
    } else if (path.endsWith('.json')) {
      res.set('Content-Type', 'application/json; charset=utf-8');
    }
  }
}));

// Webflow API helper
async function webflowFetch(endpoint) {
  const res = await fetch(`https://api.webflow.com/v2/${endpoint}`, {
    headers: {
      'Authorization': `Bearer ${WEBFLOW_TOKEN}`,
      'accept': 'application/json'
    }
  });
  return res.json();
}

// Dynamic manifest
app.get('/manifest.json', (req, res) => {
  res.json({
    "$schema": "https://pzp.riscent.com/schemas/manifest.schema.json",
    "version": "1.0.0",
    "protocolVersion": "1.0.0",
    "name": SITE_NAME,
    "description": "Content from " + SITE_NAME,
    "type": "content",
    "updated": new Date().toISOString(),
    "tokenBudgets": {
      "essential": 400,
      "full": 2000
    },
    "endpoints": {
      "context": "/context/",
      "actions": "/actions/",
      "data": "/data/"
    },
    "capabilities": ["read", "search"]
  });
});

// List CMS collections
app.get('/api/collections', async (req, res) => {
  try {
    const data = await webflowFetch(`sites/${WEBFLOW_SITE_ID}/collections`);
    res.json({
      collections: data.collections.map(c => ({
        id: c.id,
        name: c.displayName,
        slug: c.slug,
        itemsPath: `/api/collections/${c.id}/items`
      }))
    });
  } catch (error) {
    res.status(500).json({ error: 'fetch_failed' });
  }
});

// Get collection items
app.get('/api/collections/:id/items', async (req, res) => {
  try {
    const { id } = req.params;
    const { limit = 20, offset = 0 } = req.query;

    const data = await webflowFetch(
      `collections/${id}/items?limit=${limit}&offset=${offset}`
    );

    res.json({
      items: data.items.map(formatItem),
      pagination: data.pagination,
      total: data.pagination.total
    });
  } catch (error) {
    res.status(500).json({ error: 'fetch_failed' });
  }
});

// Search across collections
app.get('/api/search', async (req, res) => {
  try {
    const { query, collection } = req.query;

    // Get all collections or specific one
    const collectionsData = await webflowFetch(
      `sites/${WEBFLOW_SITE_ID}/collections`
    );

    let collections = collectionsData.collections;
    if (collection) {
      collections = collections.filter(c => c.slug === collection);
    }

    // Search items in each collection
    const allResults = [];
    for (const col of collections) {
      const items = await webflowFetch(`collections/${col.id}/items`);

      const matches = items.items.filter(item => {
        const searchText = JSON.stringify(item.fieldData).toLowerCase();
        return searchText.includes(query.toLowerCase());
      });

      allResults.push(...matches.map(item => ({
        ...formatItem(item),
        collection: col.slug
      })));
    }

    res.json({
      results: allResults,
      total: allResults.length,
      query
    });
  } catch (error) {
    res.status(500).json({ error: 'search_failed' });
  }
});

// Format CMS item
function formatItem(item) {
  return {
    id: item.id,
    slug: item.fieldData.slug,
    name: item.fieldData.name,
    ...item.fieldData,
    createdOn: item.createdOn,
    updatedOn: item.lastUpdated
  };
}

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`PZP server running on port ${PORT}`);
});
```

### 3. Static Files

**public/context/essential.md:**
```markdown
# [Site Name] Essential Context

## What This Is

[Description of your Webflow site and its content]

## Content Collections

| Collection | Description |
|------------|-------------|
| Blog Posts | Articles and updates |
| Projects | Portfolio items |
| Team | Team member profiles |

## Primary Actions

| Action | Description |
|--------|-------------|
| list-collections | List all CMS collections |
| get-items | Get items from a collection |
| search | Search across all content |
```

**public/actions/index.json:**
```json
{
  "$schema": "https://pzp.riscent.com/schemas/actions.schema.json",
  "version": "1.0.0",
  "baseUrl": "https://pzp.yoursite.com",
  "actions": [
    {
      "name": "list-collections",
      "description": "List all CMS collections",
      "endpoint": "/api/collections",
      "method": "GET",
      "input": {},
      "output": {
        "schema": {
          "collections": "array of collection objects"
        }
      }
    },
    {
      "name": "get-items",
      "description": "Get items from a collection",
      "endpoint": "/api/collections/{id}/items",
      "method": "GET",
      "input": {
        "id": {
          "type": "string",
          "required": true,
          "description": "Collection ID"
        },
        "limit": {
          "type": "integer",
          "default": 20
        },
        "offset": {
          "type": "integer",
          "default": 0
        }
      }
    },
    {
      "name": "search",
      "description": "Search all content",
      "endpoint": "/api/search",
      "method": "GET",
      "input": {
        "query": {
          "type": "string",
          "required": true
        },
        "collection": {
          "type": "string",
          "description": "Filter by collection slug"
        }
      }
    }
  ]
}
```

### 4. Get Webflow API Token

1. Go to Webflow Dashboard → Site Settings → Apps & Integrations
2. Generate API token with CMS read access
3. Note your Site ID from site settings

### 5. Deploy

```bash
# Set environment variables
export WEBFLOW_TOKEN=your_token
export WEBFLOW_SITE_ID=your_site_id
export SITE_NAME="Your Site"

# Deploy to Vercel
vercel deploy --prod
```

### 6. Configure DNS

Add CNAME record:
```
pzp → cname.vercel-dns.com
```

---

## Static Generation

For simpler sites, generate static PZP files:

**generate-pzp.js:**
```javascript
const fetch = require('node-fetch');
const fs = require('fs');

const WEBFLOW_TOKEN = process.env.WEBFLOW_TOKEN;
const WEBFLOW_SITE_ID = process.env.WEBFLOW_SITE_ID;

async function webflowFetch(endpoint) {
  const res = await fetch(`https://api.webflow.com/v2/${endpoint}`, {
    headers: {
      'Authorization': `Bearer ${WEBFLOW_TOKEN}`,
      'accept': 'application/json'
    }
  });
  return res.json();
}

async function generatePZP() {
  // Get collections
  const collectionsData = await webflowFetch(
    `sites/${WEBFLOW_SITE_ID}/collections`
  );

  const allContent = {
    collections: [],
    generated: new Date().toISOString()
  };

  for (const collection of collectionsData.collections) {
    // Get items
    const itemsData = await webflowFetch(
      `collections/${collection.id}/items?limit=100`
    );

    const collectionData = {
      id: collection.id,
      name: collection.displayName,
      slug: collection.slug,
      items: itemsData.items.map(item => ({
        id: item.id,
        slug: item.fieldData.slug,
        name: item.fieldData.name,
        ...item.fieldData
      }))
    };

    allContent.collections.push(collectionData);

    // Save collection file
    fs.writeFileSync(
      `public/data/${collection.slug}.json`,
      JSON.stringify(collectionData, null, 2)
    );
  }

  // Save index
  fs.writeFileSync(
    'public/data/index.json',
    JSON.stringify({
      collections: allContent.collections.map(c => ({
        id: c.id,
        name: c.name,
        slug: c.slug,
        itemCount: c.items.length,
        path: `/data/${c.slug}.json`
      })),
      generated: allContent.generated
    }, null, 2)
  );

  console.log(`Generated ${allContent.collections.length} collections`);
}

generatePZP();
```

---

## Webflow Webhook Sync

Keep PZP updated when Webflow content changes:

### 1. Create Webhook Endpoint

```javascript
app.post('/webhooks/webflow', async (req, res) => {
  const { triggerType, payload } = req.body;

  switch (triggerType) {
    case 'collection_item_created':
    case 'collection_item_changed':
      await regenerateCollection(payload.collectionId);
      break;
    case 'collection_item_deleted':
      await regenerateCollection(payload.collectionId);
      break;
  }

  res.status(200).send('OK');
});
```

### 2. Configure in Webflow

In Site Settings → Integrations → Webhooks:
- Add webhook URL: `https://pzp.yoursite.com/webhooks/webflow`
- Select triggers: Collection item events

---

## Testing

```bash
# Test manifest
curl https://pzp.yoursite.com/manifest.json | jq .

# Test collections
curl https://pzp.yoursite.com/api/collections | jq .

# Test items
curl https://pzp.yoursite.com/api/collections/COLLECTION_ID/items | jq .

# Test search
curl "https://pzp.yoursite.com/api/search?query=design" | jq .
```

---

## Limitations

| Feature | Webflow Support |
|---------|-----------------|
| Custom robots.txt | No |
| Custom headers | Limited (via code) |
| JSON file hosting | No (use embeds) |
| API access | Yes (CMS API) |
| Subdomain | Yes |

**Recommendation:** Always use subdomain approach for production PZP on Webflow.

---

*Webflow: Beautiful design, machine-readable content.*
