# Documentation Site PZP Implementation

> How to implement PZP for technical documentation, API references, and knowledge bases.

## Overview

Documentation sites are natural fits for PZP. Developers increasingly use AI assistants to navigate documentation, find code examples, and understand APIs. Without PZP, agents parse HTML documentation inefficiently. With PZP, documentation becomes instantly consumable.

---

## Why Documentation Needs PZP

### The Current Problem

When a developer asks AI "how do I authenticate with the Acme API":
- Agent searches documentation site
- Agent downloads HTML pages
- Agent strips navigation, sidebars, footers
- Agent guesses at code blocks and structure
- Agent may return outdated or wrong information

### The PZP Solution

With PZP:
- Agent reads structured documentation directly
- Code examples are clearly marked
- Versions are explicit
- Navigation is predictable
- Content is always current

---

## Documentation PZP Structure

```
pzp.docs.example.com/
├── index.md                    # Docs entry point
├── manifest.json               # Docs manifest
├── robots.txt                  # Discovery
│
├── context/
│   ├── essential.md            # API/product overview
│   ├── full.md                 # Complete documentation index
│   └── topics/
│       ├── authentication.md   # Auth guide
│       ├── errors.md           # Error handling
│       └── rate-limits.md      # Rate limiting
│
├── actions/
│   └── index.json              # Search, get-doc actions
│
├── data/
│   ├── index.json              # Documentation index
│   ├── versions.json           # Available versions
│   └── docs/
│       ├── getting-started.md
│       ├── authentication.md
│       ├── api-reference/
│       │   ├── index.md
│       │   ├── users.md
│       │   ├── products.md
│       │   └── orders.md
│       └── guides/
│           ├── webhooks.md
│           └── pagination.md
│
└── meta/
    └── version.json
```

---

## Key Actions for Documentation

### Search Documentation

```json
{
  "name": "search-docs",
  "description": "Search documentation content",
  "endpoint": "/api/search",
  "method": "GET",
  "input": {
    "query": { "type": "string", "required": true },
    "section": {
      "type": "string",
      "enum": ["guides", "api-reference", "tutorials"],
      "description": "Limit to section"
    },
    "version": { "type": "string", "default": "latest" }
  },
  "output": {
    "schema": {
      "results": "array of matching sections",
      "total": "number of matches"
    }
  }
}
```

### Get Documentation Page

```json
{
  "name": "get-doc",
  "description": "Get specific documentation page",
  "endpoint": "/data/docs/{path}",
  "method": "GET",
  "input": {
    "path": { "type": "string", "required": true },
    "version": { "type": "string", "default": "latest" }
  },
  "output": {
    "type": "text/markdown"
  }
}
```

### List API Endpoints

```json
{
  "name": "list-endpoints",
  "description": "List all API endpoints",
  "endpoint": "/data/api-reference/index.json",
  "method": "GET",
  "input": {},
  "output": {
    "schema": {
      "endpoints": "array of endpoint definitions"
    }
  }
}
```

---

## Documentation Schema

### Page Metadata

```json
{
  "pages": [
    {
      "path": "getting-started",
      "title": "Getting Started",
      "description": "Quick start guide for the API",
      "section": "guides",
      "order": 1,
      "tokens": 800,
      "lastUpdated": "2026-03-01",
      "tags": ["quickstart", "setup"]
    },
    {
      "path": "api-reference/users",
      "title": "Users API",
      "description": "Create, read, update, delete users",
      "section": "api-reference",
      "order": 1,
      "tokens": 1500,
      "lastUpdated": "2026-03-05",
      "tags": ["api", "users", "crud"]
    }
  ]
}
```

### API Endpoint Definition

```json
{
  "endpoints": [
    {
      "method": "GET",
      "path": "/api/users",
      "summary": "List all users",
      "description": "Returns paginated list of users",
      "authentication": "required",
      "parameters": [
        {
          "name": "limit",
          "in": "query",
          "type": "integer",
          "default": 20
        },
        {
          "name": "offset",
          "in": "query",
          "type": "integer",
          "default": 0
        }
      ],
      "responses": {
        "200": {
          "description": "Successful response",
          "schema": {
            "users": "array",
            "total": "integer"
          }
        }
      },
      "example": {
        "request": "GET /api/users?limit=10",
        "response": {
          "users": [{"id": "1", "name": "John"}],
          "total": 100
        }
      },
      "docPath": "/data/docs/api-reference/users.md"
    }
  ]
}
```

---

## Version Management

### Multiple Versions

```json
{
  "versions": [
    {
      "version": "2.0",
      "status": "current",
      "released": "2026-01-15",
      "basePath": "/v2/"
    },
    {
      "version": "1.5",
      "status": "supported",
      "released": "2025-06-01",
      "basePath": "/v1.5/",
      "deprecationDate": "2026-06-01"
    },
    {
      "version": "1.0",
      "status": "deprecated",
      "released": "2024-01-01",
      "basePath": "/v1/",
      "sunsetDate": "2025-12-31"
    }
  ],
  "default": "2.0"
}
```

### Version-Specific Access

```
/data/docs/v2/authentication.md      # Current version
/data/docs/v1.5/authentication.md    # Supported version
/data/docs/authentication.md         # Alias to current
```

---

## Context Examples

### Essential Context for API Docs

```markdown
# Acme API Essential Context

## What This Is

The Acme API provides programmatic access to Acme services. This documentation covers authentication, endpoints, and best practices for integration.

## API Basics

| Item | Value |
|------|-------|
| Base URL | https://api.acme.com/v2 |
| Authentication | Bearer token |
| Format | JSON |
| Rate Limit | 100 req/min |

## Key Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| /users | GET | List users |
| /users/{id} | GET | Get user |
| /products | GET | List products |
| /orders | POST | Create order |

## Quick Links

- Authentication: [/data/docs/authentication.md](/data/docs/authentication.md)
- API Reference: [/data/docs/api-reference/](/data/docs/api-reference/)
- Error Codes: [/data/docs/errors.md](/data/docs/errors.md)

## Primary Actions

| Action | Description |
|--------|-------------|
| search-docs | Search documentation |
| get-doc | Get specific page |
| list-endpoints | List API endpoints |
```

---

## Code Block Handling

### Markdown Code Blocks

```markdown
## Authentication

Use Bearer token authentication:

\`\`\`bash
curl -X GET "https://api.acme.com/v2/users" \
  -H "Authorization: Bearer YOUR_API_KEY"
\`\`\`

Response:

\`\`\`json
{
  "users": [
    {"id": "1", "name": "John", "email": "john@example.com"}
  ],
  "total": 1
}
\`\`\`
```

### Language-Tagged Examples

```json
{
  "examples": {
    "curl": "curl -X GET 'https://api.acme.com/v2/users' -H 'Authorization: Bearer TOKEN'",
    "python": "import requests\nresponse = requests.get('https://api.acme.com/v2/users', headers={'Authorization': 'Bearer TOKEN'})",
    "javascript": "const response = await fetch('https://api.acme.com/v2/users', { headers: { 'Authorization': 'Bearer TOKEN' } });"
  }
}
```

---

## Documentation Types

### API Reference

Comprehensive endpoint documentation:
- Method and path
- Parameters (query, path, body)
- Request/response schemas
- Code examples
- Error responses

### Guides

Conceptual and how-to documentation:
- Step-by-step instructions
- Best practices
- Architecture explanations
- Use case walkthroughs

### Tutorials

Learning-oriented documentation:
- Complete working examples
- Progressive complexity
- Hands-on exercises

### Changelog

Version history:
- New features
- Breaking changes
- Deprecations
- Migration guides

---

## Search Optimization

### Structured Search Index

```json
{
  "searchIndex": [
    {
      "path": "/data/docs/authentication.md",
      "title": "Authentication",
      "content": "Use Bearer tokens to authenticate API requests...",
      "headings": ["Getting an API Key", "Using the Token", "Token Expiration"],
      "keywords": ["auth", "token", "bearer", "api key"],
      "section": "guides"
    }
  ]
}
```

### Search Response

```json
{
  "results": [
    {
      "path": "/data/docs/authentication.md",
      "title": "Authentication",
      "snippet": "...Use Bearer tokens to authenticate API requests. Include the token in the Authorization header...",
      "relevance": 0.95,
      "section": "guides"
    }
  ],
  "total": 1,
  "query": "how to authenticate"
}
```

---

## Integration Patterns

### OpenAPI/Swagger Integration

Generate PZP from OpenAPI spec:

```javascript
const yaml = require('js-yaml');
const fs = require('fs');

function generatePZPFromOpenAPI(specPath) {
  const spec = yaml.load(fs.readFileSync(specPath, 'utf8'));

  // Generate endpoints index
  const endpoints = Object.entries(spec.paths).flatMap(([path, methods]) =>
    Object.entries(methods).map(([method, details]) => ({
      method: method.toUpperCase(),
      path,
      summary: details.summary,
      description: details.description,
      parameters: details.parameters,
      responses: details.responses,
    }))
  );

  // Write to PZP structure
  fs.writeFileSync(
    'pzp/data/api-reference/index.json',
    JSON.stringify({ endpoints }, null, 2)
  );
}
```

### Docusaurus Integration

Sync Docusaurus content to PZP:

```javascript
// Generate PZP from Docusaurus docs
const docs = fs.readdirSync('docs', { recursive: true })
  .filter(f => f.endsWith('.md'));

docs.forEach(doc => {
  const content = fs.readFileSync(`docs/${doc}`, 'utf8');
  const { data, content: body } = matter(content);

  // Strip Docusaurus-specific syntax
  const pzpContent = body
    .replace(/import .* from .*;/g, '')
    .replace(/<Tabs>.*<\/Tabs>/gs, '');

  fs.writeFileSync(`pzp/data/docs/${doc}`, pzpContent);
});
```

---

## Success Metrics

| Metric | Description |
|--------|-------------|
| Search queries | Documentation searches via PZP |
| Page fetches | Most accessed documentation pages |
| Version distribution | Which versions are being queried |
| Query patterns | What developers are looking for |

---

*Documentation that AI can navigate. Knowledge that agents can find.*
