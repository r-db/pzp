# Next.js PZP Implementation

> Add PZP to your Next.js application in 15 minutes.

## Overview

Next.js is ideal for PZP because it supports:
- Static file serving
- API routes for dynamic content
- Middleware for headers
- Easy deployment to Vercel

This guide covers both App Router (Next.js 13+) and Pages Router patterns.

---

## Quick Start

### 1. Create PZP Directory

In your Next.js project root:

```bash
mkdir -p public/pzp/context public/pzp/actions public/pzp/data
```

### 2. Add Core Files

**public/pzp/manifest.json:**
```json
{
  "$schema": "https://pzp.riscent.com/schemas/manifest.schema.json",
  "version": "1.0.0",
  "protocolVersion": "1.0.0",
  "name": "Your Site Name",
  "description": "What your site does",
  "type": "service",
  "updated": "2026-03-06T00:00:00Z",
  "tokenBudgets": {
    "essential": 400,
    "full": 2000
  },
  "endpoints": {
    "context": "/pzp/context/",
    "actions": "/pzp/actions/",
    "data": "/pzp/data/"
  },
  "capabilities": ["read", "search"]
}
```

**public/pzp/index.md:**
```markdown
# Your Site Name

> Brief description of your site.

## Identity

- **Type:** service
- **Version:** 1.0.0

## Quick Start

1. Context: [/pzp/context/essential.md](/pzp/context/essential.md)
2. Actions: [/pzp/actions/index.json](/pzp/actions/index.json)
```

**public/pzp/robots.txt:**
```
User-agent: *
Allow: /

X-PZP-Site: true
X-PZP-Manifest: /pzp/manifest.json
X-PZP-Version: 1.0.0
```

**public/pzp/context/essential.md:**
```markdown
# Essential Context

## What This Is

[2-3 sentences about your site]

## Capabilities

- [Capability 1]
- [Capability 2]

## Primary Actions

| Action | Description |
|--------|-------------|
| [action] | [description] |
```

### 3. Configure Headers

**next.config.js:**
```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  async headers() {
    return [
      {
        source: '/pzp/:path*',
        headers: [
          { key: 'X-PZP-Site', value: 'true' },
          { key: 'X-PZP-Version', value: '1.0.0' },
          { key: 'Access-Control-Allow-Origin', value: '*' },
        ],
      },
      {
        source: '/pzp/:path*.md',
        headers: [
          { key: 'Content-Type', value: 'text/markdown; charset=utf-8' },
        ],
      },
      {
        source: '/pzp/:path*.json',
        headers: [
          { key: 'Content-Type', value: 'application/json; charset=utf-8' },
        ],
      },
    ];
  },
};

module.exports = nextConfig;
```

### 4. Test Locally

```bash
npm run dev
curl http://localhost:3000/pzp/manifest.json
curl -I http://localhost:3000/pzp/index.md
```

---

## Subdomain Setup (Recommended)

For a dedicated PZP subdomain (`pzp.yourdomain.com`):

### Option A: Separate Deployment

Create a separate Next.js project for PZP:

```
your-org/
├── main-site/          # Human site
└── pzp-site/           # PZP site
```

**pzp-site/next.config.js:**
```javascript
const nextConfig = {
  async headers() {
    return [
      {
        source: '/:path*',
        headers: [
          { key: 'X-PZP-Site', value: 'true' },
          { key: 'X-PZP-Version', value: '1.0.0' },
          { key: 'Access-Control-Allow-Origin', value: '*' },
        ],
      },
    ];
  },
};

module.exports = nextConfig;
```

### Option B: Middleware Routing

Handle subdomain in middleware:

**middleware.ts:**
```typescript
import { NextResponse } from 'next/server';
import type { NextRequest } from 'next/server';

export function middleware(request: NextRequest) {
  const hostname = request.headers.get('host') || '';

  // Check if this is the PZP subdomain
  if (hostname.startsWith('pzp.')) {
    // Rewrite to /pzp path
    const url = request.nextUrl.clone();
    url.pathname = `/pzp${url.pathname}`;
    return NextResponse.rewrite(url);
  }

  return NextResponse.next();
}

export const config = {
  matcher: ['/((?!api|_next/static|_next/image|favicon.ico).*)'],
};
```

---

## Dynamic PZP Content

### API Routes for Actions

**app/api/pzp/search/route.ts (App Router):**
```typescript
import { NextRequest, NextResponse } from 'next/server';

export async function GET(request: NextRequest) {
  const searchParams = request.nextUrl.searchParams;
  const query = searchParams.get('query') || '';
  const limit = parseInt(searchParams.get('limit') || '20');

  // Your search logic
  const results = await searchProducts(query, limit);

  return NextResponse.json({
    results,
    total: results.length,
    query,
  }, {
    headers: {
      'X-PZP-Site': 'true',
      'X-PZP-Version': '1.0.0',
      'Cache-Control': 'public, max-age=300',
    },
  });
}
```

**pages/api/pzp/search.ts (Pages Router):**
```typescript
import type { NextApiRequest, NextApiResponse } from 'next';

export default async function handler(
  req: NextApiRequest,
  res: NextApiResponse
) {
  const { query = '', limit = '20' } = req.query;

  const results = await searchProducts(
    query as string,
    parseInt(limit as string)
  );

  res.setHeader('X-PZP-Site', 'true');
  res.setHeader('X-PZP-Version', '1.0.0');
  res.setHeader('Cache-Control', 'public, max-age=300');

  res.json({
    results,
    total: results.length,
    query,
  });
}
```

### Generated Static Content

**scripts/generate-pzp.ts:**
```typescript
import fs from 'fs';
import path from 'path';

interface Product {
  id: string;
  name: string;
  price: number;
}

async function generatePZP() {
  // Fetch your data
  const products = await fetchProducts();

  // Generate products JSON
  const productsJson = {
    products: products.map(p => ({
      id: p.id,
      name: p.name,
      price: p.price,
    })),
    total: products.length,
    generated: new Date().toISOString(),
  };

  // Write to public/pzp/data
  const outputPath = path.join(process.cwd(), 'public/pzp/data/products.json');
  fs.writeFileSync(outputPath, JSON.stringify(productsJson, null, 2));

  // Update manifest timestamp
  const manifestPath = path.join(process.cwd(), 'public/pzp/manifest.json');
  const manifest = JSON.parse(fs.readFileSync(manifestPath, 'utf-8'));
  manifest.updated = new Date().toISOString();
  fs.writeFileSync(manifestPath, JSON.stringify(manifest, null, 2));

  console.log(`Generated PZP data: ${products.length} products`);
}

generatePZP();
```

**package.json:**
```json
{
  "scripts": {
    "build": "npm run generate-pzp && next build",
    "generate-pzp": "ts-node scripts/generate-pzp.ts"
  }
}
```

---

## Complete Example Structure

```
your-nextjs-app/
├── app/                        # App Router
│   ├── page.tsx                # Human home page
│   └── api/
│       └── pzp/
│           ├── search/
│           │   └── route.ts    # Dynamic search
│           └── products/
│               └── [id]/
│                   └── route.ts # Dynamic product
│
├── public/
│   └── pzp/                    # Static PZP content
│       ├── index.md
│       ├── manifest.json
│       ├── robots.txt
│       ├── context/
│       │   ├── essential.md
│       │   └── full.md
│       ├── actions/
│       │   └── index.json
│       └── data/
│           ├── categories.json
│           └── products.json   # Generated
│
├── scripts/
│   └── generate-pzp.ts         # PZP generation
│
├── middleware.ts               # Subdomain handling
├── next.config.js              # Headers config
└── package.json
```

---

## Actions Configuration

**public/pzp/actions/index.json:**
```json
{
  "$schema": "https://pzp.riscent.com/schemas/actions.schema.json",
  "version": "1.0.0",
  "baseUrl": "https://pzp.yourdomain.com",
  "actions": [
    {
      "name": "search",
      "description": "Search products",
      "endpoint": "/api/pzp/search",
      "method": "GET",
      "input": {
        "query": { "type": "string", "required": true },
        "limit": { "type": "integer", "default": 20 }
      }
    },
    {
      "name": "get-product",
      "description": "Get product details",
      "endpoint": "/api/pzp/products/{id}",
      "method": "GET",
      "input": {
        "id": { "type": "string", "required": true }
      }
    }
  ]
}
```

---

## Vercel Deployment

### vercel.json (if not using next.config.js headers)

```json
{
  "headers": [
    {
      "source": "/pzp/(.*)",
      "headers": [
        { "key": "X-PZP-Site", "value": "true" },
        { "key": "X-PZP-Version", "value": "1.0.0" },
        { "key": "Access-Control-Allow-Origin", "value": "*" }
      ]
    }
  ]
}
```

### Custom Domain

1. Deploy to Vercel
2. Add custom domain `pzp.yourdomain.com`
3. Configure DNS CNAME → `cname.vercel-dns.com`

---

## TypeScript Types

**types/pzp.ts:**
```typescript
export interface PZPManifest {
  $schema: string;
  version: string;
  protocolVersion: string;
  name: string;
  description: string;
  type: 'service' | 'documentation' | 'commerce' | 'content' | 'api' | 'data';
  updated: string;
  tokenBudgets: {
    essential: number;
    full: number;
  };
  endpoints: {
    context: string;
    actions?: string;
    data?: string;
  };
  capabilities: string[];
}

export interface PZPAction {
  name: string;
  description: string;
  endpoint: string;
  method: 'GET' | 'POST' | 'PUT' | 'DELETE';
  input: Record<string, PZPParameter>;
  output?: {
    type: string;
    schema?: Record<string, string>;
  };
}

export interface PZPParameter {
  type: 'string' | 'number' | 'integer' | 'boolean' | 'array' | 'object';
  required?: boolean;
  description?: string;
  default?: any;
  enum?: string[];
}
```

---

## Testing

```bash
# Test manifest
curl http://localhost:3000/pzp/manifest.json | jq .

# Test headers
curl -I http://localhost:3000/pzp/index.md | grep -i pzp

# Test search action
curl "http://localhost:3000/api/pzp/search?query=widget" | jq .

# Verify CORS
curl -I -X OPTIONS http://localhost:3000/pzp/manifest.json
```

---

## Common Issues

### Markdown served as HTML

Add Content-Type header in next.config.js or vercel.json.

### CORS errors

Ensure `Access-Control-Allow-Origin: *` header is set.

### API routes not found

Check file naming: `route.ts` for App Router, `[...].ts` for Pages Router.

### Static files not updating

Clear `.next` cache: `rm -rf .next && npm run build`

---

*Next.js + PZP = Full-stack machine-native capability.*
