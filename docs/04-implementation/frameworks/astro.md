# Astro PZP Implementation

> The simplest path to a PZP site. Static-first, zero JavaScript overhead.

## Overview

Astro is perfect for PZP because:
- Static by default (exactly what PZP needs)
- Zero client JavaScript
- Native Markdown support
- Simple deployment anywhere

Most PZP sites can be pure Astro with no dynamic endpoints.

---

## Quick Start

### 1. Create Astro Project

```bash
npm create astro@latest pzp-site
cd pzp-site
```

Choose: Empty project, No TypeScript (or Yes), No frameworks needed.

### 2. Create PZP Structure

```bash
mkdir -p src/pages/context src/pages/actions src/pages/data src/pages/meta
```

### 3. Add Core Files

**src/pages/index.md:**
```markdown
---
layout: ../layouts/PZPLayout.astro
---

# Your Site Name

> Brief description of what this site does.

## Identity

- **Type:** documentation
- **Version:** 1.0.0
- **Updated:** 2026-03-06

## Quick Start

1. Context: [/context/essential](/context/essential)
2. Actions: [/actions/index.json](/actions/index.json)

## Navigation

| Resource | Path |
|----------|------|
| Essential Context | [/context/essential](/context/essential) |
| Full Context | [/context/full](/context/full) |
| Actions | [/actions/index.json](/actions/index.json) |
```

**src/pages/manifest.json.ts:**
```typescript
import type { APIRoute } from 'astro';

export const GET: APIRoute = () => {
  const manifest = {
    "$schema": "https://pzp.riscent.com/schemas/manifest.schema.json",
    "version": "1.0.0",
    "protocolVersion": "1.0.0",
    "name": "Your Site Name",
    "description": "What your site does",
    "type": "documentation",
    "updated": new Date().toISOString(),
    "tokenBudgets": {
      "essential": 400,
      "full": 2000
    },
    "endpoints": {
      "context": "/context/",
      "actions": "/actions/"
    },
    "capabilities": ["read"]
  };

  return new Response(JSON.stringify(manifest, null, 2), {
    headers: {
      'Content-Type': 'application/json; charset=utf-8',
      'X-PZP-Site': 'true',
      'X-PZP-Version': '1.0.0',
    },
  });
};
```

**src/pages/robots.txt.ts:**
```typescript
import type { APIRoute } from 'astro';

export const GET: APIRoute = () => {
  const robotsTxt = `User-agent: *
Allow: /

X-PZP-Site: true
X-PZP-Manifest: /manifest.json
X-PZP-Version: 1.0.0

Sitemap: /sitemap.txt`;

  return new Response(robotsTxt, {
    headers: {
      'Content-Type': 'text/plain; charset=utf-8',
    },
  });
};
```

**src/pages/context/essential.md:**
```markdown
---
layout: ../../layouts/PZPLayout.astro
---

# Essential Context

## What This Is

[2-3 sentences describing your site/service]

## Key Capabilities

- [Capability 1]
- [Capability 2]
- [Capability 3]

## Primary Actions

| Action | Description |
|--------|-------------|
| [action-1] | [what it does] |
```

**src/pages/context/full.md:**
```markdown
---
layout: ../../layouts/PZPLayout.astro
---

# Full Context

## Overview

[Detailed description - 200-500 words]

## Complete Capabilities

### [Category 1]

[Description]

### [Category 2]

[Description]

## Constraints

- [Constraint 1]
- [Constraint 2]
```

### 4. Create Layout

**src/layouts/PZPLayout.astro:**
```astro
---
// This layout outputs raw markdown content
// No HTML wrapper needed for PZP
const { frontmatter } = Astro.props;
---
<slot />
```

Wait - for pure markdown output, we need a different approach:

**src/pages/context/essential.md.ts:**
```typescript
import type { APIRoute } from 'astro';

const content = `# Essential Context

## What This Is

[Your description here]

## Key Capabilities

- Capability 1
- Capability 2

## Primary Actions

| Action | Description |
|--------|-------------|
| action-1 | What it does |
`;

export const GET: APIRoute = () => {
  return new Response(content, {
    headers: {
      'Content-Type': 'text/markdown; charset=utf-8',
      'X-PZP-Site': 'true',
      'X-PZP-Version': '1.0.0',
    },
  });
};
```

### 5. Add Actions

**src/pages/actions/index.json.ts:**
```typescript
import type { APIRoute } from 'astro';

export const GET: APIRoute = () => {
  const actions = {
    "$schema": "https://pzp.riscent.com/schemas/actions.schema.json",
    "version": "1.0.0",
    "baseUrl": "https://pzp.yourdomain.com",
    "actions": [
      {
        "name": "list-content",
        "description": "List all available content",
        "endpoint": "/data/index.json",
        "method": "GET",
        "input": {}
      }
    ]
  };

  return new Response(JSON.stringify(actions, null, 2), {
    headers: {
      'Content-Type': 'application/json; charset=utf-8',
      'X-PZP-Site': 'true',
    },
  });
};
```

---

## Static Markdown Approach

For simpler setups, use the `public` folder:

```
your-astro-project/
├── public/
│   ├── index.md
│   ├── manifest.json
│   ├── robots.txt
│   ├── context/
│   │   ├── essential.md
│   │   └── full.md
│   ├── actions/
│   │   └── index.json
│   └── data/
│       └── content.json
└── astro.config.mjs
```

Then configure headers in your hosting platform.

---

## Astro Configuration

**astro.config.mjs:**
```javascript
import { defineConfig } from 'astro/config';

export default defineConfig({
  site: 'https://pzp.yourdomain.com',
  output: 'static', // or 'server' for SSR

  // Custom headers (if using Astro SSR)
  server: {
    headers: {
      'X-PZP-Site': 'true',
      'X-PZP-Version': '1.0.0',
    },
  },
});
```

---

## Content Collections (Recommended)

For sites with many articles/pages, use Astro Content Collections:

**src/content/config.ts:**
```typescript
import { defineCollection, z } from 'astro:content';

const articles = defineCollection({
  type: 'content',
  schema: z.object({
    title: z.string(),
    description: z.string(),
    author: z.string(),
    published: z.date(),
    category: z.string(),
    tags: z.array(z.string()),
  }),
});

export const collections = { articles };
```

**src/content/articles/my-article.md:**
```markdown
---
title: "Article Title"
description: "Brief description"
author: "Author Name"
published: 2026-03-06
category: "technology"
tags: ["pzp", "web"]
---

Article content here...
```

**src/pages/data/articles.json.ts:**
```typescript
import type { APIRoute } from 'astro';
import { getCollection } from 'astro:content';

export const GET: APIRoute = async () => {
  const articles = await getCollection('articles');

  const data = {
    articles: articles.map(article => ({
      slug: article.slug,
      title: article.data.title,
      description: article.data.description,
      author: article.data.author,
      published: article.data.published.toISOString(),
      category: article.data.category,
      tags: article.data.tags,
      contentPath: `/data/articles/${article.slug}.md`,
    })),
    total: articles.length,
    generated: new Date().toISOString(),
  };

  return new Response(JSON.stringify(data, null, 2), {
    headers: {
      'Content-Type': 'application/json; charset=utf-8',
      'X-PZP-Site': 'true',
    },
  });
};
```

---

## Complete Project Structure

```
pzp-astro-site/
├── src/
│   ├── content/
│   │   ├── config.ts
│   │   └── articles/
│   │       └── *.md
│   └── pages/
│       ├── index.md.ts           # Entry point
│       ├── manifest.json.ts      # Manifest
│       ├── robots.txt.ts         # Discovery
│       ├── sitemap.txt.ts        # File list
│       ├── context/
│       │   ├── essential.md.ts
│       │   └── full.md.ts
│       ├── actions/
│       │   └── index.json.ts
│       └── data/
│           ├── index.json.ts
│           └── articles/
│               └── [...slug].md.ts
├── astro.config.mjs
└── package.json
```

---

## Deployment

### Vercel

```bash
npm run build
vercel deploy
```

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
    },
    {
      "source": "/(.*).md",
      "headers": [
        { "key": "Content-Type", "value": "text/markdown; charset=utf-8" }
      ]
    }
  ]
}
```

### Netlify

**netlify.toml:**
```toml
[[headers]]
  for = "/*"
  [headers.values]
    X-PZP-Site = "true"
    X-PZP-Version = "1.0.0"
    Access-Control-Allow-Origin = "*"

[[headers]]
  for = "/*.md"
  [headers.values]
    Content-Type = "text/markdown; charset=utf-8"
```

### Cloudflare Pages

```bash
npm run build
wrangler pages deploy dist
```

**_headers:**
```
/*
  X-PZP-Site: true
  X-PZP-Version: 1.0.0
  Access-Control-Allow-Origin: *

/*.md
  Content-Type: text/markdown; charset=utf-8
```

---

## Testing

```bash
# Build and preview
npm run build
npm run preview

# Test endpoints
curl http://localhost:4321/manifest.json | jq .
curl http://localhost:4321/context/essential.md
curl -I http://localhost:4321/ | grep -i pzp
```

---

## Tips

### Markdown with Frontmatter

If you want to keep frontmatter in your markdown but strip it for PZP:

```typescript
import type { APIRoute } from 'astro';
import fs from 'fs';
import matter from 'gray-matter';

export const GET: APIRoute = () => {
  const file = fs.readFileSync('./src/content/context/essential.md', 'utf-8');
  const { content } = matter(file);

  return new Response(content, {
    headers: {
      'Content-Type': 'text/markdown; charset=utf-8',
    },
  });
};
```

### Dynamic Routes

For article content:

**src/pages/data/articles/[slug].md.ts:**
```typescript
import type { APIRoute, GetStaticPaths } from 'astro';
import { getCollection } from 'astro:content';

export const getStaticPaths: GetStaticPaths = async () => {
  const articles = await getCollection('articles');
  return articles.map(article => ({
    params: { slug: article.slug },
  }));
};

export const GET: APIRoute = async ({ params }) => {
  const articles = await getCollection('articles');
  const article = articles.find(a => a.slug === params.slug);

  if (!article) {
    return new Response('Not found', { status: 404 });
  }

  return new Response(article.body, {
    headers: {
      'Content-Type': 'text/markdown; charset=utf-8',
      'X-PZP-Site': 'true',
    },
  });
};
```

---

*Astro: The fastest path to PZP. Build once, serve forever.*
