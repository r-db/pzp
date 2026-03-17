# Framework Implementation Guides

> Step-by-step PZP implementation for every major framework.

## Overview

These guides show how to add PZP to projects built with popular frameworks. Each guide includes:
- Project structure
- File generation
- Routing configuration
- Deployment setup

---

## Available Guides

### JavaScript/TypeScript

| Framework | Type | Guide |
|-----------|------|-------|
| [Next.js](nextjs.md) | Full-stack React | Available |
| [Astro](astro.md) | Static/SSR | Available |
| [Express](express.md) | Node.js backend | Available |
| Nuxt | Full-stack Vue | Coming soon |
| SvelteKit | Full-stack Svelte | Coming soon |
| Remix | Full-stack React | Coming soon |

### Python

| Framework | Type | Guide |
|-----------|------|-------|
| FastAPI | API framework | Coming soon |
| Django | Full-stack | Coming soon |

### Ruby

| Framework | Type | Guide |
|-----------|------|-------|
| Rails | Full-stack | Coming soon |

### Go

| Framework | Type | Guide |
|-----------|------|-------|
| Standard lib | Native Go | Coming soon |

### PHP

| Framework | Type | Guide |
|-----------|------|-------|
| Laravel | Full-stack | Coming soon |

---

## Quick Comparison

| Framework | Static PZP | Dynamic PZP | Complexity |
|-----------|------------|-------------|------------|
| Next.js | Yes | Yes | Low |
| Astro | Yes | Limited | Very Low |
| Express | Yes | Yes | Low |
| Nuxt | Yes | Yes | Low |
| FastAPI | Yes | Yes | Low |
| Django | Yes | Yes | Medium |
| Rails | Yes | Yes | Medium |
| Laravel | Yes | Yes | Medium |

**Static PZP:** Pre-generated files at build time
**Dynamic PZP:** Generated on request from database/CMS

---

## General Pattern

Regardless of framework, PZP implementation follows this pattern:

### 1. Create PZP Directory Structure

```
your-project/
├── src/
│   └── ... (your app code)
└── pzp/                    # PZP content
    ├── index.md
    ├── manifest.json
    ├── robots.txt
    ├── context/
    ├── actions/
    └── data/
```

### 2. Configure Routes

Serve PZP content from subdomain or path:

```
pzp.yourdomain.com/*  →  /pzp/*
```

### 3. Set Headers

Every PZP response needs:

```http
X-PZP-Site: true
X-PZP-Version: 1.0.0
Content-Type: text/markdown; charset=utf-8  # for .md
Content-Type: application/json; charset=utf-8  # for .json
Access-Control-Allow-Origin: *
```

### 4. Deploy

Deploy PZP alongside (or separate from) your human site.

---

## Choosing an Approach

### Static Generation (Recommended for most)

Generate PZP files at build time. Best for:
- Content that changes infrequently
- Maximum performance
- Simple deployment

**Frameworks:** Astro, Next.js (static export), any SSG

### Server-Side Generation

Generate PZP content on request. Best for:
- Frequently changing content
- Personalized responses
- Large catalogs

**Frameworks:** Next.js (API routes), Express, FastAPI, Django

### Hybrid

Static structure + dynamic data endpoints. Best for:
- E-commerce (static catalog, dynamic inventory)
- Content sites (static articles, dynamic comments)

---

## Common Patterns

### Subdomain Setup

```
example.com          →  Human site
pzp.example.com      →  PZP site (separate deployment)
```

### Path-Based Setup

```
example.com/*        →  Human site
example.com/pzp/*    →  PZP content (same deployment)
```

### Middleware for Headers

```javascript
// Express/Next.js middleware
app.use('/pzp', (req, res, next) => {
  res.set('X-PZP-Site', 'true');
  res.set('X-PZP-Version', '1.0.0');
  res.set('Access-Control-Allow-Origin', '*');
  next();
});
```

---

## Next Steps

1. Choose your framework guide
2. Follow the step-by-step instructions
3. Deploy and verify with the [5-minute setup](../quickstart/5-minute.md) checklist

---

*Every framework. Same protocol. Maximum reach.*
