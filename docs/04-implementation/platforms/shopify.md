# Shopify PZP Implementation

> Add PZP to your Shopify store. Make products discoverable by AI agents.

## Overview

Shopify powers 28% of e-commerce sites. AI shopping agents need to discover and recommend products. Without PZP, agents struggle to understand your catalog. With PZP, agents can search, compare, and recommend your products accurately.

**Recommended approach:** Subdomain with API sync

Shopify's platform limitations (no custom headers on root domain, limited static file hosting) make a subdomain approach the best choice.

---

## Architecture

```
yourstore.myshopify.com       → Human storefront
pzp.yourstore.com             → PZP site (Vercel/Netlify)
                              ↑
                        Shopify Admin API
                        (product sync)
```

---

## Quick Start

### 1. Create PZP Project

```bash
mkdir pzp-shopify && cd pzp-shopify
npm init -y
npm install express cors node-fetch
```

### 2. Create Server

**server.js:**
```javascript
const express = require('express');
const cors = require('cors');
const fetch = require('node-fetch');
const fs = require('fs');
const path = require('path');

const app = express();

// Configuration
const SHOPIFY_STORE = process.env.SHOPIFY_STORE; // yourstore.myshopify.com
const SHOPIFY_TOKEN = process.env.SHOPIFY_ACCESS_TOKEN;
const SHOP_NAME = process.env.SHOP_NAME || 'Your Store';

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

// Serve static PZP files
app.use(express.static('public', {
  setHeaders: (res, filePath) => {
    if (filePath.endsWith('.md')) {
      res.set('Content-Type', 'text/markdown; charset=utf-8');
    } else if (filePath.endsWith('.json')) {
      res.set('Content-Type', 'application/json; charset=utf-8');
    }
  }
}));

// Dynamic manifest
app.get('/manifest.json', (req, res) => {
  res.json({
    "$schema": "https://pzp.riscent.com/schemas/manifest.schema.json",
    "version": "1.0.0",
    "protocolVersion": "1.0.0",
    "name": SHOP_NAME,
    "description": `Shop ${SHOP_NAME} products. Search, browse, and discover.`,
    "type": "commerce",
    "updated": new Date().toISOString(),
    "tokenBudgets": {
      "essential": 500,
      "full": 2500
    },
    "endpoints": {
      "context": "/context/",
      "actions": "/actions/",
      "data": "/data/"
    },
    "capabilities": ["read", "search", "query"]
  });
});

// Shopify API helper
async function shopifyFetch(endpoint) {
  const res = await fetch(
    `https://${SHOPIFY_STORE}/admin/api/2024-01/${endpoint}`,
    {
      headers: {
        'X-Shopify-Access-Token': SHOPIFY_TOKEN,
        'Content-Type': 'application/json'
      }
    }
  );
  return res.json();
}

// Search products
app.get('/api/search', async (req, res) => {
  try {
    const { query, collection, minPrice, maxPrice, limit = 20 } = req.query;

    // Build Shopify search query
    let searchQuery = query || '';
    if (collection) {
      searchQuery += ` collection:${collection}`;
    }

    const data = await shopifyFetch(
      `products.json?limit=${limit}&title=${encodeURIComponent(query || '')}`
    );

    let products = data.products.map(formatProduct);

    // Apply filters
    if (minPrice) {
      products = products.filter(p => p.price.amount >= parseFloat(minPrice));
    }
    if (maxPrice) {
      products = products.filter(p => p.price.amount <= parseFloat(maxPrice));
    }

    res.json({
      results: products,
      total: products.length,
      query,
      filters: { collection, minPrice, maxPrice }
    });
  } catch (error) {
    res.status(500).json({ error: 'search_failed', message: error.message });
  }
});

// Get single product
app.get('/api/products/:id', async (req, res) => {
  try {
    const data = await shopifyFetch(`products/${req.params.id}.json`);
    res.json(formatProduct(data.product, true));
  } catch (error) {
    res.status(404).json({ error: 'not_found' });
  }
});

// List collections
app.get('/api/collections', async (req, res) => {
  try {
    const data = await shopifyFetch('collections.json');
    res.json({
      collections: data.collections.map(c => ({
        id: c.id,
        handle: c.handle,
        title: c.title,
        description: c.body_html?.replace(/<[^>]*>/g, ''),
        productsCount: c.products_count
      }))
    });
  } catch (error) {
    res.status(500).json({ error: 'fetch_failed' });
  }
});

// Get collection products
app.get('/api/collections/:handle/products', async (req, res) => {
  try {
    // First get collection ID
    const collections = await shopifyFetch(
      `collections.json?handle=${req.params.handle}`
    );
    const collection = collections.collections[0];

    if (!collection) {
      return res.status(404).json({ error: 'collection_not_found' });
    }

    // Get products in collection
    const data = await shopifyFetch(
      `collections/${collection.id}/products.json?limit=${req.query.limit || 20}`
    );

    res.json({
      collection: {
        id: collection.id,
        handle: collection.handle,
        title: collection.title
      },
      products: data.products.map(formatProduct),
      total: data.products.length
    });
  } catch (error) {
    res.status(500).json({ error: 'fetch_failed' });
  }
});

// Format product for PZP
function formatProduct(product, full = false) {
  const variant = product.variants[0];

  const formatted = {
    id: product.id,
    handle: product.handle,
    title: product.title,
    description: full
      ? product.body_html?.replace(/<[^>]*>/g, '')
      : product.body_html?.replace(/<[^>]*>/g, '').substring(0, 200),
    price: {
      amount: parseFloat(variant.price),
      currency: 'USD',
      compareAt: variant.compare_at_price
        ? parseFloat(variant.compare_at_price)
        : null
    },
    available: product.status === 'active' && variant.inventory_quantity > 0,
    inventory: variant.inventory_quantity,
    vendor: product.vendor,
    productType: product.product_type,
    tags: product.tags?.split(', ') || [],
    url: `https://${SHOPIFY_STORE}/products/${product.handle}`
  };

  if (full) {
    formatted.variants = product.variants.map(v => ({
      id: v.id,
      title: v.title,
      price: parseFloat(v.price),
      available: v.inventory_quantity > 0,
      sku: v.sku,
      options: {
        option1: v.option1,
        option2: v.option2,
        option3: v.option3
      }
    }));

    formatted.images = product.images.map(img => ({
      src: img.src,
      alt: img.alt
    }));

    formatted.options = product.options.map(opt => ({
      name: opt.name,
      values: opt.values
    }));
  }

  return formatted;
}

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`PZP server running on port ${PORT}`);
});
```

### 3. Create Static Files

**public/index.md:**
```markdown
# [Store Name] PZP Interface

> Machine-native access to our product catalog.

## Identity

- **Type:** E-commerce
- **Version:** 1.0.0
- **Platform:** Shopify

## Quick Start

1. Context: [/context/essential.md](/context/essential.md)
2. Actions: [/actions/index.json](/actions/index.json)
3. Search: Use `search-products` action

## Capabilities

- **Search:** Find products by name, type, or tags
- **Browse:** View collections and categories
- **Query:** Filter by price, availability
- **Read:** Get detailed product information
```

**public/robots.txt:**
```
User-agent: *
Allow: /

X-PZP-Site: true
X-PZP-Manifest: /manifest.json
X-PZP-Version: 1.0.0

Sitemap: /sitemap.txt
```

**public/context/essential.md:**
```markdown
# [Store Name] Essential Context

## What This Is

[Store Name] is an online store selling [product types]. Browse our catalog, search for products, and check availability through this PZP interface.

## Product Categories

| Category | Description |
|----------|-------------|
| [Category 1] | [Description] |
| [Category 2] | [Description] |
| [Category 3] | [Description] |

## Pricing

- Currency: USD
- Free shipping: Orders over $50
- Returns: 30-day policy

## Primary Actions

| Action | Description |
|--------|-------------|
| search-products | Search products by keyword |
| get-product | Get detailed product info |
| list-collections | Browse collections |
| collection-products | Products in a collection |

## Quick Links

- Collections: [/api/collections](/api/collections)
- Search: [/api/search?query=...](/api/search)
```

**public/actions/index.json:**
```json
{
  "$schema": "https://pzp.riscent.com/schemas/actions.schema.json",
  "version": "1.0.0",
  "baseUrl": "https://pzp.yourstore.com",
  "actions": [
    {
      "name": "search-products",
      "description": "Search products by keyword",
      "endpoint": "/api/search",
      "method": "GET",
      "input": {
        "query": {
          "type": "string",
          "required": true,
          "description": "Search term"
        },
        "collection": {
          "type": "string",
          "description": "Filter by collection handle"
        },
        "minPrice": {
          "type": "number",
          "description": "Minimum price filter"
        },
        "maxPrice": {
          "type": "number",
          "description": "Maximum price filter"
        },
        "limit": {
          "type": "integer",
          "default": 20,
          "description": "Max results"
        }
      },
      "output": {
        "schema": {
          "results": "array of products",
          "total": "number of matches"
        }
      }
    },
    {
      "name": "get-product",
      "description": "Get detailed product information",
      "endpoint": "/api/products/{id}",
      "method": "GET",
      "input": {
        "id": {
          "type": "string",
          "required": true,
          "description": "Product ID"
        }
      },
      "output": {
        "schema": {
          "id": "product ID",
          "title": "product name",
          "description": "full description",
          "price": "price object",
          "variants": "available variants",
          "images": "product images"
        }
      }
    },
    {
      "name": "list-collections",
      "description": "List all product collections",
      "endpoint": "/api/collections",
      "method": "GET",
      "input": {},
      "output": {
        "schema": {
          "collections": "array of collections"
        }
      }
    },
    {
      "name": "collection-products",
      "description": "Get products in a collection",
      "endpoint": "/api/collections/{handle}/products",
      "method": "GET",
      "input": {
        "handle": {
          "type": "string",
          "required": true,
          "description": "Collection handle"
        },
        "limit": {
          "type": "integer",
          "default": 20
        }
      },
      "output": {
        "schema": {
          "collection": "collection info",
          "products": "array of products"
        }
      }
    }
  ]
}
```

### 4. Set Up Shopify Private App

1. Go to Shopify Admin → Apps → Develop apps
2. Create a new app
3. Configure Admin API access:
   - `read_products`
   - `read_inventory`
   - `read_product_listings`
4. Install app and get access token

### 5. Deploy

**Set environment variables:**
```bash
export SHOPIFY_STORE=yourstore.myshopify.com
export SHOPIFY_ACCESS_TOKEN=shpat_xxxxx
export SHOP_NAME="Your Store Name"
```

**Deploy to Vercel:**

**vercel.json:**
```json
{
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        { "key": "X-PZP-Site", "value": "true" },
        { "key": "X-PZP-Version", "value": "1.0.0" },
        { "key": "Access-Control-Allow-Origin", "value": "*" }
      ]
    }
  ]
}
```

```bash
vercel deploy --prod
```

### 6. Configure DNS

Add CNAME record:
```
pzp → cname.vercel-dns.com
```

---

## Static Generation Alternative

For simpler setups, generate static PZP files:

**generate-pzp.js:**
```javascript
const fetch = require('node-fetch');
const fs = require('fs');

const SHOPIFY_STORE = process.env.SHOPIFY_STORE;
const SHOPIFY_TOKEN = process.env.SHOPIFY_ACCESS_TOKEN;

async function shopifyFetch(endpoint) {
  const res = await fetch(
    `https://${SHOPIFY_STORE}/admin/api/2024-01/${endpoint}`,
    {
      headers: {
        'X-Shopify-Access-Token': SHOPIFY_TOKEN,
        'Content-Type': 'application/json'
      }
    }
  );
  return res.json();
}

async function generatePZP() {
  // Fetch all products
  const products = [];
  let url = 'products.json?limit=250';

  while (url) {
    const data = await shopifyFetch(url);
    products.push(...data.products);

    // Handle pagination (simplified)
    url = null; // Add proper pagination handling
  }

  // Generate products index
  const productsIndex = {
    products: products.map(p => ({
      id: p.id,
      handle: p.handle,
      title: p.title,
      description: p.body_html?.replace(/<[^>]*>/g, '').substring(0, 200),
      price: {
        amount: parseFloat(p.variants[0].price),
        currency: 'USD'
      },
      available: p.status === 'active',
      vendor: p.vendor,
      productType: p.product_type,
      tags: p.tags?.split(', ') || [],
      path: `/data/products/${p.handle}.json`
    })),
    total: products.length,
    generated: new Date().toISOString()
  };

  fs.mkdirSync('public/data/products', { recursive: true });
  fs.writeFileSync(
    'public/data/products.json',
    JSON.stringify(productsIndex, null, 2)
  );

  // Generate individual product files
  for (const product of products) {
    const productData = {
      id: product.id,
      handle: product.handle,
      title: product.title,
      description: product.body_html?.replace(/<[^>]*>/g, ''),
      price: {
        amount: parseFloat(product.variants[0].price),
        compareAt: product.variants[0].compare_at_price
          ? parseFloat(product.variants[0].compare_at_price)
          : null,
        currency: 'USD'
      },
      available: product.status === 'active',
      variants: product.variants.map(v => ({
        id: v.id,
        title: v.title,
        price: parseFloat(v.price),
        sku: v.sku,
        available: v.inventory_quantity > 0
      })),
      images: product.images.map(img => ({
        src: img.src,
        alt: img.alt
      })),
      vendor: product.vendor,
      productType: product.product_type,
      tags: product.tags?.split(', ') || [],
      url: `https://${SHOPIFY_STORE}/products/${product.handle}`
    };

    fs.writeFileSync(
      `public/data/products/${product.handle}.json`,
      JSON.stringify(productData, null, 2)
    );
  }

  // Fetch collections
  const collectionsData = await shopifyFetch('collections.json');
  const collections = {
    collections: collectionsData.collections.map(c => ({
      id: c.id,
      handle: c.handle,
      title: c.title,
      description: c.body_html?.replace(/<[^>]*>/g, '')
    })),
    total: collectionsData.collections.length
  };

  fs.writeFileSync(
    'public/data/collections.json',
    JSON.stringify(collections, null, 2)
  );

  // Update manifest
  const manifest = JSON.parse(fs.readFileSync('public/manifest.json', 'utf8'));
  manifest.updated = new Date().toISOString();
  fs.writeFileSync('public/manifest.json', JSON.stringify(manifest, null, 2));

  console.log(`Generated PZP: ${products.length} products, ${collections.collections.length} collections`);
}

generatePZP();
```

**Run on deploy or schedule:**
```bash
node generate-pzp.js
```

---

## Webhook Sync

Keep PZP updated with Shopify webhooks:

**Webhook endpoint:**
```javascript
app.post('/webhooks/shopify', async (req, res) => {
  const hmac = req.headers['x-shopify-hmac-sha256'];

  // Verify webhook (implement HMAC verification)
  if (!verifyWebhook(req.body, hmac)) {
    return res.status(401).send('Unauthorized');
  }

  const topic = req.headers['x-shopify-topic'];

  switch (topic) {
    case 'products/create':
    case 'products/update':
      await updateProductFile(req.body);
      break;
    case 'products/delete':
      await deleteProductFile(req.body.id);
      break;
    case 'inventory_levels/update':
      await updateInventory(req.body);
      break;
  }

  res.status(200).send('OK');
});
```

**Register webhooks:**
```bash
# Using Shopify CLI
shopify webhooks create --topic products/create --address https://pzp.yourstore.com/webhooks/shopify
shopify webhooks create --topic products/update --address https://pzp.yourstore.com/webhooks/shopify
shopify webhooks create --topic products/delete --address https://pzp.yourstore.com/webhooks/shopify
```

---

## Testing

```bash
# Test manifest
curl https://pzp.yourstore.com/manifest.json | jq .

# Test headers
curl -I https://pzp.yourstore.com/ | grep -i pzp

# Test search
curl "https://pzp.yourstore.com/api/search?query=shirt" | jq .

# Test product
curl https://pzp.yourstore.com/api/products/12345678 | jq .

# Test collections
curl https://pzp.yourstore.com/api/collections | jq .
```

---

## Best Practices

### 1. Cache API Responses

```javascript
const NodeCache = require('node-cache');
const cache = new NodeCache({ stdTTL: 300 });

app.get('/api/search', async (req, res) => {
  const cacheKey = `search:${JSON.stringify(req.query)}`;
  const cached = cache.get(cacheKey);

  if (cached) {
    return res.json(cached);
  }

  const results = await searchProducts(req.query);
  cache.set(cacheKey, results);
  res.json(results);
});
```

### 2. Handle Rate Limits

Shopify API has rate limits. Implement backoff:

```javascript
async function shopifyFetch(endpoint, retries = 3) {
  try {
    const res = await fetch(/* ... */);

    if (res.status === 429) {
      const retryAfter = res.headers.get('Retry-After') || 2;
      await new Promise(r => setTimeout(r, retryAfter * 1000));
      return shopifyFetch(endpoint, retries - 1);
    }

    return res.json();
  } catch (error) {
    if (retries > 0) {
      await new Promise(r => setTimeout(r, 1000));
      return shopifyFetch(endpoint, retries - 1);
    }
    throw error;
  }
}
```

### 3. Include Inventory Status

```javascript
// Real-time inventory check
app.get('/api/products/:id/availability', async (req, res) => {
  const data = await shopifyFetch(
    `inventory_levels.json?inventory_item_ids=${req.params.id}`
  );

  res.json({
    available: data.inventory_levels.some(l => l.available > 0),
    levels: data.inventory_levels.map(l => ({
      locationId: l.location_id,
      available: l.available
    }))
  });
});
```

---

## Product Schema Reference

```json
{
  "id": "123456789",
  "handle": "awesome-product",
  "title": "Awesome Product",
  "description": "Product description text...",
  "price": {
    "amount": 29.99,
    "compareAt": 39.99,
    "currency": "USD"
  },
  "available": true,
  "inventory": 50,
  "vendor": "Brand Name",
  "productType": "Clothing",
  "tags": ["sale", "featured", "new"],
  "variants": [
    {
      "id": "987654321",
      "title": "Small / Blue",
      "price": 29.99,
      "sku": "AWE-S-BLU",
      "available": true,
      "options": {
        "size": "Small",
        "color": "Blue"
      }
    }
  ],
  "images": [
    {
      "src": "https://cdn.shopify.com/...",
      "alt": "Product image"
    }
  ],
  "options": [
    { "name": "Size", "values": ["Small", "Medium", "Large"] },
    { "name": "Color", "values": ["Blue", "Red", "Green"] }
  ],
  "url": "https://yourstore.com/products/awesome-product"
}
```

---

*Shopify + PZP: Products that AI agents can discover and recommend.*
