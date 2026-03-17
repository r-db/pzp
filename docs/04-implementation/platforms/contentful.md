# Contentful PZP Implementation

> Generate PZP from your Contentful content. Headless CMS meets machine-native access.

## Overview

Contentful is a headless CMS, making it ideal for PZP. You already have structured content—PZP just provides another delivery format alongside your website and apps.

**Approaches:**
1. **Build-time generation:** Generate PZP during static site build
2. **API proxy:** Real-time PZP from Contentful API
3. **Hybrid:** Static context files + dynamic data

---

## Quick Start: Build-Time Generation

### 1. Install Dependencies

```bash
npm install contentful
```

### 2. Create Generator Script

**scripts/generate-pzp.js:**
```javascript
const contentful = require('contentful');
const fs = require('fs');
const path = require('path');

const client = contentful.createClient({
  space: process.env.CONTENTFUL_SPACE_ID,
  accessToken: process.env.CONTENTFUL_ACCESS_TOKEN
});

async function generatePZP() {
  const pzpDir = './public/pzp';

  // Ensure directories exist
  ['context', 'actions', 'data'].forEach(dir => {
    fs.mkdirSync(path.join(pzpDir, dir), { recursive: true });
  });

  // Get all content types
  const contentTypes = await client.getContentTypes();

  // Generate manifest
  const manifest = {
    "$schema": "https://pzp.riscent.com/schemas/manifest.schema.json",
    "version": "1.0.0",
    "protocolVersion": "1.0.0",
    "name": "Your Site Name",
    "description": "Content from Contentful",
    "type": "content",
    "updated": new Date().toISOString(),
    "tokenBudgets": {
      "essential": 500,
      "full": 2500
    },
    "endpoints": {
      "context": "/pzp/context/",
      "actions": "/pzp/actions/",
      "data": "/pzp/data/"
    },
    "capabilities": ["read", "search"]
  };

  fs.writeFileSync(
    path.join(pzpDir, 'manifest.json'),
    JSON.stringify(manifest, null, 2)
  );

  // Generate content type index
  const contentIndex = {
    contentTypes: contentTypes.items.map(ct => ({
      id: ct.sys.id,
      name: ct.name,
      description: ct.description,
      fields: ct.fields.map(f => ({
        id: f.id,
        name: f.name,
        type: f.type
      })),
      dataPath: `/pzp/data/${ct.sys.id}.json`
    })),
    generated: new Date().toISOString()
  };

  fs.writeFileSync(
    path.join(pzpDir, 'data', 'index.json'),
    JSON.stringify(contentIndex, null, 2)
  );

  // Generate data for each content type
  for (const contentType of contentTypes.items) {
    const entries = await client.getEntries({
      content_type: contentType.sys.id,
      limit: 1000
    });

    const data = {
      contentType: contentType.sys.id,
      entries: entries.items.map(entry => formatEntry(entry, contentType)),
      total: entries.total,
      generated: new Date().toISOString()
    };

    fs.writeFileSync(
      path.join(pzpDir, 'data', `${contentType.sys.id}.json`),
      JSON.stringify(data, null, 2)
    );

    console.log(`Generated ${entries.items.length} entries for ${contentType.sys.id}`);
  }

  // Generate essential context
  const essentialContext = generateEssentialContext(contentTypes, contentIndex);
  fs.writeFileSync(
    path.join(pzpDir, 'context', 'essential.md'),
    essentialContext
  );

  // Generate actions
  const actions = generateActions(contentTypes);
  fs.writeFileSync(
    path.join(pzpDir, 'actions', 'index.json'),
    JSON.stringify(actions, null, 2)
  );

  // Generate robots.txt
  const robotsTxt = `User-agent: *
Allow: /

X-PZP-Site: true
X-PZP-Manifest: /pzp/manifest.json
X-PZP-Version: 1.0.0

Sitemap: /pzp/sitemap.txt`;

  fs.writeFileSync(path.join(pzpDir, 'robots.txt'), robotsTxt);

  console.log('PZP generation complete!');
}

function formatEntry(entry, contentType) {
  const fields = {};

  for (const field of contentType.fields) {
    const value = entry.fields[field.id];
    if (value) {
      fields[field.id] = formatFieldValue(value, field);
    }
  }

  return {
    id: entry.sys.id,
    createdAt: entry.sys.createdAt,
    updatedAt: entry.sys.updatedAt,
    ...fields
  };
}

function formatFieldValue(value, field) {
  // Handle localized content (get default locale)
  if (typeof value === 'object' && value['en-US']) {
    value = value['en-US'];
  }

  switch (field.type) {
    case 'RichText':
      return richTextToMarkdown(value);
    case 'Link':
      if (field.linkType === 'Asset') {
        return value?.fields?.file?.url;
      }
      return value?.sys?.id;
    case 'Array':
      return value.map(v => formatFieldValue(v, { type: field.items?.type }));
    default:
      return value;
  }
}

function richTextToMarkdown(richText) {
  if (!richText || !richText.content) return '';

  return richText.content.map(node => {
    switch (node.nodeType) {
      case 'paragraph':
        return node.content.map(c => c.value || '').join('') + '\n\n';
      case 'heading-1':
        return '# ' + node.content.map(c => c.value || '').join('') + '\n\n';
      case 'heading-2':
        return '## ' + node.content.map(c => c.value || '').join('') + '\n\n';
      case 'heading-3':
        return '### ' + node.content.map(c => c.value || '').join('') + '\n\n';
      case 'unordered-list':
        return node.content.map(li =>
          '- ' + li.content.map(c =>
            c.content?.map(t => t.value || '').join('') || ''
          ).join('')
        ).join('\n') + '\n\n';
      default:
        return '';
    }
  }).join('');
}

function generateEssentialContext(contentTypes, contentIndex) {
  let context = `# Site Essential Context

## What This Is

Content managed in Contentful, accessible via PZP.

## Content Types

| Type | Description | Entries |
|------|-------------|---------|
`;

  for (const ct of contentIndex.contentTypes) {
    context += `| ${ct.name} | ${ct.description || '-'} | [Data](${ct.dataPath}) |\n`;
  }

  context += `
## Primary Actions

| Action | Description |
|--------|-------------|
| list-content-types | List all content types |
| get-entries | Get entries by content type |
| search | Search all content |
`;

  return context;
}

function generateActions(contentTypes) {
  const actions = {
    "$schema": "https://pzp.riscent.com/schemas/actions.schema.json",
    "version": "1.0.0",
    "baseUrl": "https://pzp.yoursite.com",
    "actions": [
      {
        "name": "list-content-types",
        "description": "List all available content types",
        "endpoint": "/pzp/data/index.json",
        "method": "GET",
        "input": {}
      },
      {
        "name": "get-entries",
        "description": "Get entries for a content type",
        "endpoint": "/pzp/data/{contentType}.json",
        "method": "GET",
        "input": {
          "contentType": {
            "type": "string",
            "required": true,
            "enum": contentTypes.items.map(ct => ct.sys.id)
          }
        }
      }
    ]
  };

  return actions;
}

generatePZP().catch(console.error);
```

### 3. Add to Build Process

**package.json:**
```json
{
  "scripts": {
    "build": "npm run generate-pzp && next build",
    "generate-pzp": "node scripts/generate-pzp.js"
  }
}
```

### 4. Environment Variables

```bash
CONTENTFUL_SPACE_ID=your_space_id
CONTENTFUL_ACCESS_TOKEN=your_delivery_api_token
```

---

## Real-Time API Proxy

For dynamic content that changes frequently:

**server.js:**
```javascript
const express = require('express');
const contentful = require('contentful');
const cors = require('cors');

const app = express();

const client = contentful.createClient({
  space: process.env.CONTENTFUL_SPACE_ID,
  accessToken: process.env.CONTENTFUL_ACCESS_TOKEN
});

// PZP middleware
app.use(cors());
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
    }
  }
}));

// Dynamic manifest
app.get('/manifest.json', async (req, res) => {
  const space = await client.getSpace();

  res.json({
    "$schema": "https://pzp.riscent.com/schemas/manifest.schema.json",
    "version": "1.0.0",
    "protocolVersion": "1.0.0",
    "name": space.name,
    "description": `Content from ${space.name}`,
    "type": "content",
    "updated": new Date().toISOString(),
    "tokenBudgets": {
      "essential": 500,
      "full": 2500
    },
    "endpoints": {
      "context": "/context/",
      "actions": "/actions/",
      "data": "/api/"
    },
    "capabilities": ["read", "search", "query"]
  });
});

// List content types
app.get('/api/content-types', async (req, res) => {
  try {
    const contentTypes = await client.getContentTypes();

    res.json({
      contentTypes: contentTypes.items.map(ct => ({
        id: ct.sys.id,
        name: ct.name,
        description: ct.description,
        fields: ct.fields.map(f => ({
          id: f.id,
          name: f.name,
          type: f.type,
          required: f.required
        }))
      }))
    });
  } catch (error) {
    res.status(500).json({ error: 'fetch_failed' });
  }
});

// Get entries
app.get('/api/entries/:contentType', async (req, res) => {
  try {
    const { contentType } = req.params;
    const { limit = 20, skip = 0, order } = req.query;

    const entries = await client.getEntries({
      content_type: contentType,
      limit: parseInt(limit),
      skip: parseInt(skip),
      order: order || '-sys.createdAt'
    });

    res.json({
      entries: entries.items.map(formatEntry),
      total: entries.total,
      skip: entries.skip,
      limit: entries.limit
    });
  } catch (error) {
    res.status(500).json({ error: 'fetch_failed' });
  }
});

// Get single entry
app.get('/api/entries/:contentType/:id', async (req, res) => {
  try {
    const entry = await client.getEntry(req.params.id);
    res.json(formatEntry(entry));
  } catch (error) {
    res.status(404).json({ error: 'not_found' });
  }
});

// Search entries
app.get('/api/search', async (req, res) => {
  try {
    const { query, contentType, limit = 20 } = req.query;

    const params = {
      query,
      limit: parseInt(limit)
    };

    if (contentType) {
      params.content_type = contentType;
    }

    const entries = await client.getEntries(params);

    res.json({
      results: entries.items.map(formatEntry),
      total: entries.total,
      query
    });
  } catch (error) {
    res.status(500).json({ error: 'search_failed' });
  }
});

function formatEntry(entry) {
  return {
    id: entry.sys.id,
    contentType: entry.sys.contentType?.sys?.id,
    createdAt: entry.sys.createdAt,
    updatedAt: entry.sys.updatedAt,
    fields: entry.fields
  };
}

const PORT = process.env.PORT || 3000;
app.listen(PORT);
```

---

## Webhook Integration

Regenerate PZP when content changes:

### 1. Webhook Endpoint

```javascript
app.post('/webhooks/contentful', async (req, res) => {
  const { sys } = req.body;

  // Verify webhook (check headers)
  const secret = req.headers['x-contentful-webhook-secret'];
  if (secret !== process.env.CONTENTFUL_WEBHOOK_SECRET) {
    return res.status(401).send('Unauthorized');
  }

  // Regenerate affected content type
  if (sys.contentType) {
    await regenerateContentType(sys.contentType.sys.id);
  }

  res.status(200).send('OK');
});
```

### 2. Configure in Contentful

In Space Settings → Webhooks:
- URL: `https://pzp.yoursite.com/webhooks/contentful`
- Triggers: Entry publish, unpublish, delete
- Secret header: Your webhook secret

---

## Actions Configuration

**public/actions/index.json:**
```json
{
  "$schema": "https://pzp.riscent.com/schemas/actions.schema.json",
  "version": "1.0.0",
  "baseUrl": "https://pzp.yoursite.com",
  "actions": [
    {
      "name": "list-content-types",
      "description": "List all content types in the space",
      "endpoint": "/api/content-types",
      "method": "GET",
      "input": {},
      "output": {
        "schema": {
          "contentTypes": "array of content type definitions"
        }
      }
    },
    {
      "name": "get-entries",
      "description": "Get entries for a content type",
      "endpoint": "/api/entries/{contentType}",
      "method": "GET",
      "input": {
        "contentType": {
          "type": "string",
          "required": true,
          "description": "Content type ID"
        },
        "limit": {
          "type": "integer",
          "default": 20,
          "description": "Max entries to return"
        },
        "skip": {
          "type": "integer",
          "default": 0,
          "description": "Entries to skip"
        },
        "order": {
          "type": "string",
          "description": "Order field (prefix with - for desc)"
        }
      }
    },
    {
      "name": "get-entry",
      "description": "Get a single entry by ID",
      "endpoint": "/api/entries/{contentType}/{id}",
      "method": "GET",
      "input": {
        "contentType": {
          "type": "string",
          "required": true
        },
        "id": {
          "type": "string",
          "required": true,
          "description": "Entry ID"
        }
      }
    },
    {
      "name": "search",
      "description": "Full-text search across entries",
      "endpoint": "/api/search",
      "method": "GET",
      "input": {
        "query": {
          "type": "string",
          "required": true,
          "description": "Search query"
        },
        "contentType": {
          "type": "string",
          "description": "Filter by content type"
        },
        "limit": {
          "type": "integer",
          "default": 20
        }
      }
    }
  ]
}
```

---

## Framework Integrations

### Next.js with Contentful

**pages/api/pzp/[...path].js:**
```javascript
import { createClient } from 'contentful';

const client = createClient({
  space: process.env.CONTENTFUL_SPACE_ID,
  accessToken: process.env.CONTENTFUL_ACCESS_TOKEN
});

export default async function handler(req, res) {
  res.setHeader('X-PZP-Site', 'true');
  res.setHeader('X-PZP-Version', '1.0.0');
  res.setHeader('Access-Control-Allow-Origin', '*');

  const { path } = req.query;
  const fullPath = path.join('/');

  // Route to appropriate handler
  if (fullPath === 'manifest.json') {
    return handleManifest(req, res);
  }
  // ... other routes
}
```

### Gatsby with Contentful

Use `gatsby-source-contentful` and generate PZP in `gatsby-node.js`:

```javascript
exports.onPostBuild = async ({ graphql }) => {
  const result = await graphql(`
    query {
      allContentfulBlogPost {
        nodes {
          id
          title
          slug
          body {
            raw
          }
        }
      }
    }
  `);

  // Generate PZP files from GraphQL data
  const posts = result.data.allContentfulBlogPost.nodes;
  // ... write to public/pzp/
};
```

---

## Localization

Handle multiple locales:

```javascript
// Get entries for specific locale
app.get('/api/entries/:contentType', async (req, res) => {
  const { locale = 'en-US' } = req.query;

  const entries = await client.getEntries({
    content_type: req.params.contentType,
    locale
  });

  // Fields are already in requested locale
  res.json({ entries: entries.items.map(formatEntry) });
});
```

**Manifest with locales:**
```json
{
  "locales": {
    "available": ["en-US", "de-DE", "fr-FR"],
    "default": "en-US"
  }
}
```

---

## Testing

```bash
# Test manifest
curl https://pzp.yoursite.com/manifest.json | jq .

# Test content types
curl https://pzp.yoursite.com/api/content-types | jq .

# Test entries
curl https://pzp.yoursite.com/api/entries/blogPost | jq .

# Test search
curl "https://pzp.yoursite.com/api/search?query=hello" | jq .

# Test single entry
curl https://pzp.yoursite.com/api/entries/blogPost/ENTRY_ID | jq .
```

---

## Best Practices

### 1. Cache Responses

```javascript
const NodeCache = require('node-cache');
const cache = new NodeCache({ stdTTL: 300 });

app.get('/api/entries/:contentType', async (req, res) => {
  const cacheKey = `entries:${req.params.contentType}:${JSON.stringify(req.query)}`;

  let data = cache.get(cacheKey);
  if (!data) {
    data = await fetchEntries(req.params.contentType, req.query);
    cache.set(cacheKey, data);
  }

  res.json(data);
});
```

### 2. Handle Rich Text

```javascript
const { documentToHtmlString } = require('@contentful/rich-text-html-renderer');
const { documentToPlainTextString } = require('@contentful/rich-text-plain-text-renderer');

function formatRichText(richText) {
  return {
    html: documentToHtmlString(richText),
    text: documentToPlainTextString(richText),
    raw: richText
  };
}
```

### 3. Resolve Links

```javascript
const entries = await client.getEntries({
  content_type: 'blogPost',
  include: 2 // Resolve 2 levels of linked entries
});
```

---

*Contentful: Structured content, machine-readable delivery.*
