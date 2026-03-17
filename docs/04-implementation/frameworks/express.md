# Express PZP Implementation

> Add PZP to your Express/Node.js API in 10 minutes.

## Overview

Express is ideal for PZP when you need:
- Dynamic content from databases
- Real-time data endpoints
- Complex action implementations
- Integration with existing Node.js backends

This guide shows both standalone PZP servers and adding PZP to existing Express apps.

---

## Quick Start

### 1. Setup

```bash
mkdir pzp-express && cd pzp-express
npm init -y
npm install express cors
```

### 2. Create Server

**server.js:**
```javascript
const express = require('express');
const cors = require('cors');
const path = require('path');

const app = express();

// Middleware
app.use(cors());
app.use(express.json());

// PZP Headers Middleware
app.use((req, res, next) => {
  res.set('X-PZP-Site', 'true');
  res.set('X-PZP-Version', '1.0.0');
  next();
});

// Serve static PZP files
app.use(express.static('public'));

// Set content types
app.use((req, res, next) => {
  if (req.path.endsWith('.md')) {
    res.type('text/markdown; charset=utf-8');
  } else if (req.path.endsWith('.json')) {
    res.type('application/json; charset=utf-8');
  }
  next();
});

// Dynamic endpoints
app.get('/api/search', async (req, res) => {
  const { query, limit = 20 } = req.query;
  // Your search logic
  const results = await search(query, parseInt(limit));
  res.json({ results, total: results.length, query });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`PZP server running on port ${PORT}`);
});
```

### 3. Add Static Files

**public/manifest.json:**
```json
{
  "$schema": "https://pzp.riscent.com/schemas/manifest.schema.json",
  "version": "1.0.0",
  "protocolVersion": "1.0.0",
  "name": "Your API Name",
  "description": "What your API does",
  "type": "api",
  "updated": "2026-03-06T00:00:00Z",
  "tokenBudgets": {
    "essential": 400,
    "full": 2000
  },
  "endpoints": {
    "context": "/context/",
    "actions": "/actions/",
    "data": "/data/"
  },
  "capabilities": ["read", "search", "query"]
}
```

**public/index.md:**
```markdown
# Your API Name

> Machine-native API access.

## Quick Start

1. Context: [/context/essential.md](/context/essential.md)
2. Actions: [/actions/index.json](/actions/index.json)

## Capabilities

- Search data
- Query resources
- Real-time information
```

**public/robots.txt:**
```
User-agent: *
Allow: /

X-PZP-Site: true
X-PZP-Manifest: /manifest.json
X-PZP-Version: 1.0.0
```

**public/context/essential.md:**
```markdown
# Essential Context

## What This Is

[Description of your API]

## Primary Actions

| Action | Endpoint | Method |
|--------|----------|--------|
| search | /api/search | GET |
| get-item | /api/items/:id | GET |
```

**public/actions/index.json:**
```json
{
  "$schema": "https://pzp.riscent.com/schemas/actions.schema.json",
  "version": "1.0.0",
  "baseUrl": "https://pzp.yourdomain.com",
  "actions": [
    {
      "name": "search",
      "description": "Search all items",
      "endpoint": "/api/search",
      "method": "GET",
      "input": {
        "query": { "type": "string", "required": true },
        "limit": { "type": "integer", "default": 20 }
      }
    },
    {
      "name": "get-item",
      "description": "Get item by ID",
      "endpoint": "/api/items/{id}",
      "method": "GET",
      "input": {
        "id": { "type": "string", "required": true }
      }
    }
  ]
}
```

### 4. Test

```bash
node server.js

# In another terminal
curl http://localhost:3000/manifest.json
curl http://localhost:3000/context/essential.md
curl "http://localhost:3000/api/search?query=test"
```

---

## Complete Express Application

**Full implementation with database integration:**

```javascript
const express = require('express');
const cors = require('cors');
const path = require('path');

const app = express();

// ============================================
// Middleware
// ============================================

app.use(cors());
app.use(express.json());

// PZP Headers - applied to all routes
const pzpHeaders = (req, res, next) => {
  res.set({
    'X-PZP-Site': 'true',
    'X-PZP-Version': '1.0.0',
    'Access-Control-Allow-Origin': '*',
  });
  next();
};

app.use(pzpHeaders);

// ============================================
// Static PZP Files
// ============================================

// Serve static files with proper content types
app.use(express.static('public', {
  setHeaders: (res, filePath) => {
    if (filePath.endsWith('.md')) {
      res.set('Content-Type', 'text/markdown; charset=utf-8');
    } else if (filePath.endsWith('.json')) {
      res.set('Content-Type', 'application/json; charset=utf-8');
    }
  }
}));

// ============================================
// Dynamic Manifest (with live timestamp)
// ============================================

app.get('/manifest.json', (req, res) => {
  res.json({
    "$schema": "https://pzp.riscent.com/schemas/manifest.schema.json",
    "version": "1.0.0",
    "protocolVersion": "1.0.0",
    "name": "Product API",
    "description": "Access product catalog and inventory",
    "type": "api",
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
    "capabilities": ["read", "search", "query"],
    "rateLimits": {
      "default": { "requests": 100, "window": "1m" },
      "search": { "requests": 50, "window": "1m" }
    }
  });
});

// ============================================
// Search Endpoint
// ============================================

app.get('/api/search', async (req, res) => {
  try {
    const {
      query = '',
      category,
      minPrice,
      maxPrice,
      inStock,
      sort = 'relevance',
      limit = 20,
      offset = 0
    } = req.query;

    // Build query (example with mock data)
    let results = await getProducts(); // Your data source

    // Apply filters
    if (query) {
      results = results.filter(p =>
        p.name.toLowerCase().includes(query.toLowerCase()) ||
        p.description.toLowerCase().includes(query.toLowerCase())
      );
    }
    if (category) {
      results = results.filter(p => p.category === category);
    }
    if (minPrice) {
      results = results.filter(p => p.price >= parseFloat(minPrice));
    }
    if (maxPrice) {
      results = results.filter(p => p.price <= parseFloat(maxPrice));
    }
    if (inStock === 'true') {
      results = results.filter(p => p.inStock);
    }

    // Sort
    if (sort === 'price-asc') {
      results.sort((a, b) => a.price - b.price);
    } else if (sort === 'price-desc') {
      results.sort((a, b) => b.price - a.price);
    }

    // Paginate
    const total = results.length;
    results = results.slice(parseInt(offset), parseInt(offset) + parseInt(limit));

    res.set('Cache-Control', 'public, max-age=300');
    res.json({
      results,
      total,
      hasMore: parseInt(offset) + results.length < total,
      query,
      filters: { category, minPrice, maxPrice, inStock }
    });
  } catch (error) {
    res.status(500).json({
      error: 'search_failed',
      message: 'Search operation failed'
    });
  }
});

// ============================================
// Get Single Item
// ============================================

app.get('/api/items/:id', async (req, res) => {
  try {
    const { id } = req.params;
    const item = await getProductById(id); // Your data source

    if (!item) {
      return res.status(404).json({
        error: 'not_found',
        message: `Item ${id} not found`
      });
    }

    res.set('Cache-Control', 'public, max-age=60');
    res.json(item);
  } catch (error) {
    res.status(500).json({
      error: 'fetch_failed',
      message: 'Failed to fetch item'
    });
  }
});

// ============================================
// Data Endpoints
// ============================================

app.get('/data/categories.json', async (req, res) => {
  const categories = await getCategories();
  res.set('Cache-Control', 'public, max-age=3600');
  res.json({ categories });
});

app.get('/data/products/recent.json', async (req, res) => {
  const products = await getRecentProducts(10);
  res.set('Cache-Control', 'public, max-age=300');
  res.json({
    products,
    generated: new Date().toISOString()
  });
});

// ============================================
// Rate Limiting (optional)
// ============================================

const rateLimit = require('express-rate-limit');

const apiLimiter = rateLimit({
  windowMs: 60 * 1000, // 1 minute
  max: 100,
  standardHeaders: true,
  legacyHeaders: false,
  handler: (req, res) => {
    res.status(429).json({
      error: 'rate_limited',
      message: 'Too many requests',
      retryAfter: 60
    });
  }
});

app.use('/api/', apiLimiter);

// ============================================
// Error Handling
// ============================================

app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({
    error: 'internal_error',
    message: 'An unexpected error occurred'
  });
});

// ============================================
// Start Server
// ============================================

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`PZP API running at http://localhost:${PORT}`);
  console.log(`Manifest: http://localhost:${PORT}/manifest.json`);
});

// ============================================
// Mock Data Functions (replace with your DB)
// ============================================

async function getProducts() {
  return [
    { id: '1', name: 'Widget Pro', price: 99.99, category: 'widgets', inStock: true },
    { id: '2', name: 'Widget Basic', price: 49.99, category: 'widgets', inStock: true },
    { id: '3', name: 'Gadget X', price: 149.99, category: 'gadgets', inStock: false },
  ];
}

async function getProductById(id) {
  const products = await getProducts();
  return products.find(p => p.id === id);
}

async function getCategories() {
  return [
    { id: 'widgets', name: 'Widgets', count: 50 },
    { id: 'gadgets', name: 'Gadgets', count: 30 },
  ];
}

async function getRecentProducts(limit) {
  const products = await getProducts();
  return products.slice(0, limit);
}
```

---

## Project Structure

```
pzp-express/
├── server.js               # Main application
├── package.json
│
├── public/                 # Static PZP files
│   ├── index.md
│   ├── robots.txt
│   ├── context/
│   │   ├── essential.md
│   │   └── full.md
│   └── actions/
│       └── index.json
│
├── routes/                 # Route handlers (optional)
│   ├── search.js
│   ├── items.js
│   └── data.js
│
├── middleware/             # Custom middleware (optional)
│   ├── pzpHeaders.js
│   └── rateLimit.js
│
└── models/                 # Data models (optional)
    └── product.js
```

---

## TypeScript Version

**server.ts:**
```typescript
import express, { Request, Response, NextFunction } from 'express';
import cors from 'cors';
import path from 'path';

const app = express();

// Types
interface SearchQuery {
  query?: string;
  category?: string;
  minPrice?: string;
  maxPrice?: string;
  inStock?: string;
  sort?: 'relevance' | 'price-asc' | 'price-desc';
  limit?: string;
  offset?: string;
}

interface Product {
  id: string;
  name: string;
  description: string;
  price: number;
  category: string;
  inStock: boolean;
}

// Middleware
app.use(cors());
app.use(express.json());

app.use((req: Request, res: Response, next: NextFunction) => {
  res.set({
    'X-PZP-Site': 'true',
    'X-PZP-Version': '1.0.0',
  });
  next();
});

// Routes
app.get('/api/search', async (req: Request<{}, {}, {}, SearchQuery>, res: Response) => {
  const { query = '', limit = '20', offset = '0' } = req.query;

  const results = await searchProducts(query, parseInt(limit), parseInt(offset));

  res.json({
    results,
    total: results.length,
    query,
  });
});

app.get('/api/items/:id', async (req: Request<{ id: string }>, res: Response) => {
  const { id } = req.params;
  const item = await getProductById(id);

  if (!item) {
    return res.status(404).json({ error: 'not_found' });
  }

  res.json(item);
});

// Start
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

---

## Adding to Existing Express App

If you have an existing Express app, add PZP as a sub-router:

```javascript
const express = require('express');
const pzpRouter = require('./routes/pzp');

const app = express();

// Your existing routes
app.use('/api', yourExistingRoutes);

// Mount PZP at /pzp
app.use('/pzp', pzpRouter);

// Or mount at subdomain using vhost
const vhost = require('vhost');
app.use(vhost('pzp.yourdomain.com', pzpRouter));
```

**routes/pzp.js:**
```javascript
const express = require('express');
const router = express.Router();

// PZP headers for all routes in this router
router.use((req, res, next) => {
  res.set('X-PZP-Site', 'true');
  res.set('X-PZP-Version', '1.0.0');
  next();
});

// Static files
router.use(express.static('pzp-public'));

// Dynamic endpoints
router.get('/api/search', searchHandler);
router.get('/api/items/:id', getItemHandler);

module.exports = router;
```

---

## Deployment

### Docker

**Dockerfile:**
```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
EXPOSE 3000
CMD ["node", "server.js"]
```

### PM2

```bash
npm install -g pm2
pm2 start server.js --name pzp-api
pm2 save
```

### Vercel (Serverless)

**api/index.js:**
```javascript
const app = require('../server');
module.exports = app;
```

**vercel.json:**
```json
{
  "rewrites": [
    { "source": "/(.*)", "destination": "/api" }
  ]
}
```

---

## Testing

```bash
# Start server
npm start

# Test manifest
curl http://localhost:3000/manifest.json | jq .

# Test headers
curl -I http://localhost:3000/

# Test search
curl "http://localhost:3000/api/search?query=widget&limit=5" | jq .

# Test item
curl http://localhost:3000/api/items/1 | jq .

# Test rate limiting
for i in {1..110}; do curl -s -o /dev/null -w "%{http_code}\n" http://localhost:3000/api/search; done
```

---

*Express: Dynamic PZP with full Node.js power.*
