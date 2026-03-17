# Complete Documentation Site PZP Implementation

> A fully working PZP site for "DevKit" - a developer tools documentation platform.

## Overview

This is a complete, copy-paste-ready PZP implementation for technical documentation. DevKit is a fictional developer tools documentation site that demonstrates every aspect of documentation-focused PZP.

---

## File Structure

```
pzp.docs.devkit.io/
├── index.md                    # Entry point
├── manifest.json               # Site manifest
├── robots.txt                  # Discovery
├── sitemap.txt                 # File listing
│
├── context/
│   ├── essential.md            # Core context (~400 tokens)
│   ├── full.md                 # Complete context (~2000 tokens)
│   └── topics/
│       ├── getting-started.md  # Quick start guide
│       ├── api-overview.md     # API structure
│       ├── authentication.md   # Auth guide
│       └── errors.md           # Error reference
│
├── actions/
│   └── index.json              # Search and navigation actions
│
├── data/
│   ├── index.json              # Documentation index
│   ├── versions.json           # Available versions
│   ├── search-index.json       # Search index
│   └── docs/
│       ├── getting-started.md
│       ├── installation.md
│       ├── configuration.md
│       ├── authentication.md
│       ├── api-reference/
│       │   ├── index.md
│       │   ├── users.md
│       │   ├── projects.md
│       │   ├── tasks.md
│       │   └── webhooks.md
│       ├── guides/
│       │   ├── index.md
│       │   ├── pagination.md
│       │   ├── rate-limiting.md
│       │   └── error-handling.md
│       └── sdks/
│           ├── index.md
│           ├── javascript.md
│           ├── python.md
│           └── go.md
│
└── meta/
    └── version.json
```

---

## Core Files

### index.md

```markdown
# DevKit Documentation PZP Interface

> Machine-native access to DevKit API documentation.

## Identity

- **Name:** DevKit Documentation
- **Type:** Documentation
- **Version:** 2.0.0
- **Updated:** 2026-03-17

## What This Is

DevKit is a developer platform for building and managing applications. This PZP interface provides AI agents direct access to our API documentation, guides, and code examples.

## Quick Start

1. Read context: [/context/essential.md](/context/essential.md)
2. See docs index: [/data/index.json](/data/index.json)
3. Search docs: Use `search-docs` action

## Navigation

| Resource | Path | Description |
|----------|------|-------------|
| Essential Context | [/context/essential.md](/context/essential.md) | API overview (~400 tokens) |
| Full Context | [/context/full.md](/context/full.md) | Complete reference |
| Documentation Index | [/data/index.json](/data/index.json) | All available docs |
| API Reference | [/data/docs/api-reference/](/data/docs/api-reference/) | Endpoint documentation |
| Guides | [/data/docs/guides/](/data/docs/guides/) | How-to guides |
| SDKs | [/data/docs/sdks/](/data/docs/sdks/) | SDK documentation |

## Capabilities

- **Search:** Full-text documentation search
- **Read:** Access any documentation page
- **Navigate:** Browse by section or topic
- **Versions:** Access multiple API versions
```

---

### manifest.json

```json
{
  "$schema": "https://pzp.riscent.com/schemas/manifest.schema.json",
  "version": "2.0.0",
  "protocolVersion": "1.0.0",
  "name": "DevKit Documentation",
  "description": "Complete API documentation for DevKit developer platform. Includes API reference, guides, SDK documentation, and code examples.",
  "type": "documentation",
  "updated": "2026-03-17T00:00:00Z",
  "contact": {
    "support": "support@devkit.io",
    "documentation": "docs@devkit.io"
  },
  "tokenBudgets": {
    "essential": 400,
    "full": 2000,
    "topics": {
      "getting-started": 300,
      "api-overview": 400,
      "authentication": 350,
      "errors": 250
    }
  },
  "endpoints": {
    "context": "/context/",
    "actions": "/actions/",
    "data": "/data/"
  },
  "capabilities": [
    "read",
    "search",
    "navigate"
  ],
  "versions": {
    "current": "2.0",
    "supported": ["2.0", "1.5"],
    "deprecated": ["1.0"],
    "versionsFile": "/data/versions.json"
  },
  "authentication": {
    "required": false,
    "note": "All documentation is publicly accessible"
  },
  "rateLimits": {
    "default": {
      "requests": 200,
      "window": "1m"
    },
    "search": {
      "requests": 50,
      "window": "1m"
    }
  }
}
```

---

### robots.txt

```
User-agent: *
Allow: /

X-PZP-Site: true
X-PZP-Manifest: /manifest.json
X-PZP-Version: 2.0.0

Sitemap: /sitemap.txt
```

---

## Context Files

### context/essential.md

```markdown
# DevKit API Essential Context

## What This Is

DevKit is a developer platform for building and managing applications. The API provides programmatic access to users, projects, tasks, and webhooks with comprehensive SDKs for JavaScript, Python, and Go.

## API Basics

| Item | Value |
|------|-------|
| Base URL | `https://api.devkit.io/v2` |
| Authentication | Bearer token (API key or OAuth) |
| Format | JSON |
| Rate Limit | 1,000 req/min |

## Key Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| /users | GET, POST | List or create users |
| /users/{id} | GET, PUT, DELETE | Manage specific user |
| /projects | GET, POST | List or create projects |
| /projects/{id}/tasks | GET, POST | Project tasks |
| /webhooks | GET, POST | Webhook subscriptions |

## Authentication

```
Authorization: Bearer YOUR_API_KEY
```

Get your API key at [devkit.io/settings/api](https://devkit.io/settings/api)

## Quick Links

- Getting Started: [/data/docs/getting-started.md](/data/docs/getting-started.md)
- API Reference: [/data/docs/api-reference/index.md](/data/docs/api-reference/index.md)
- Authentication: [/data/docs/authentication.md](/data/docs/authentication.md)
- Error Codes: [/context/topics/errors.md](/context/topics/errors.md)

## Primary Actions

| Action | Description |
|--------|-------------|
| search-docs | Search all documentation |
| get-doc | Get specific documentation page |
| list-endpoints | List all API endpoints |
| get-examples | Get code examples for endpoint |
```

---

### context/full.md

```markdown
# DevKit API Full Context

## Overview

DevKit is a comprehensive developer platform that provides APIs for building and managing applications. This documentation covers everything needed to integrate with DevKit, from authentication to advanced webhook handling.

## API Architecture

### Base URLs

| Environment | URL |
|-------------|-----|
| Production | `https://api.devkit.io/v2` |
| Sandbox | `https://sandbox.api.devkit.io/v2` |
| Legacy (v1.5) | `https://api.devkit.io/v1.5` |

### Request Format

All requests must:
- Use HTTPS
- Include `Authorization` header
- Send JSON body for POST/PUT (with `Content-Type: application/json`)
- Accept `application/json` responses

### Response Format

All responses include:
- `data`: The requested resource(s)
- `meta`: Pagination, timestamps, etc.
- `errors`: Array of errors (if any)

```json
{
  "data": { ... },
  "meta": {
    "requestId": "req_abc123",
    "timestamp": "2026-03-17T12:00:00Z"
  }
}
```

## Authentication

### API Keys

Personal API keys for server-side access:

```
Authorization: Bearer sk_live_abc123...
```

- Create at: devkit.io/settings/api
- Never expose in client-side code
- Rotate regularly

### OAuth 2.0

For user-authorized access:

1. Redirect to: `https://devkit.io/oauth/authorize`
2. User approves
3. Exchange code for token at: `https://api.devkit.io/oauth/token`
4. Use token in requests

## Resources

### Users

Manage user accounts and permissions.

| Endpoint | Method | Description |
|----------|--------|-------------|
| /users | GET | List all users |
| /users | POST | Create user |
| /users/{id} | GET | Get user by ID |
| /users/{id} | PUT | Update user |
| /users/{id} | DELETE | Delete user |

### Projects

Organize work into projects.

| Endpoint | Method | Description |
|----------|--------|-------------|
| /projects | GET | List projects |
| /projects | POST | Create project |
| /projects/{id} | GET | Get project |
| /projects/{id} | PUT | Update project |
| /projects/{id}/members | GET | List members |
| /projects/{id}/tasks | GET | List tasks |

### Tasks

Track work items within projects.

| Endpoint | Method | Description |
|----------|--------|-------------|
| /projects/{id}/tasks | GET | List tasks |
| /projects/{id}/tasks | POST | Create task |
| /tasks/{id} | GET | Get task |
| /tasks/{id} | PUT | Update task |
| /tasks/{id} | DELETE | Delete task |

### Webhooks

Receive real-time event notifications.

| Endpoint | Method | Description |
|----------|--------|-------------|
| /webhooks | GET | List webhooks |
| /webhooks | POST | Create webhook |
| /webhooks/{id} | GET | Get webhook |
| /webhooks/{id} | DELETE | Delete webhook |

## Pagination

All list endpoints support pagination:

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| limit | integer | 20 | Items per page (max 100) |
| offset | integer | 0 | Number to skip |
| cursor | string | - | Cursor for cursor pagination |

Response includes:
```json
{
  "data": [...],
  "meta": {
    "total": 100,
    "limit": 20,
    "offset": 0,
    "hasMore": true
  }
}
```

## Rate Limiting

| Tier | Limit | Window |
|------|-------|--------|
| Free | 100 | 1 minute |
| Pro | 1,000 | 1 minute |
| Enterprise | 10,000 | 1 minute |

Headers returned:
- `X-RateLimit-Limit`: Your limit
- `X-RateLimit-Remaining`: Requests left
- `X-RateLimit-Reset`: Reset timestamp

## Error Handling

Standard error response:
```json
{
  "errors": [
    {
      "code": "invalid_request",
      "message": "The request was invalid",
      "field": "email",
      "details": "Email format is invalid"
    }
  ]
}
```

Common codes:
- `authentication_required`: Missing auth
- `invalid_token`: Bad or expired token
- `not_found`: Resource doesn't exist
- `rate_limited`: Too many requests
- `validation_error`: Invalid input

## SDKs

Official SDKs available:

| Language | Package | Install |
|----------|---------|---------|
| JavaScript | @devkit/sdk | `npm install @devkit/sdk` |
| Python | devkit | `pip install devkit` |
| Go | devkit-go | `go get github.com/devkit/devkit-go` |

## Documentation Structure

- **Getting Started:** Quick setup and first API call
- **API Reference:** Complete endpoint documentation
- **Guides:** How-to articles for common tasks
- **SDKs:** Language-specific SDK documentation
- **Changelog:** Version history and updates
```

---

### context/topics/authentication.md

```markdown
# DevKit Authentication

## Methods

| Method | Use Case | Security |
|--------|----------|----------|
| API Key | Server-side apps | High |
| OAuth 2.0 | User-authorized apps | High |
| Personal Token | Development/testing | Medium |

## API Keys

### Creating an API Key

1. Go to devkit.io/settings/api
2. Click "Create API Key"
3. Choose permissions (read, write, admin)
4. Copy key (shown once only)

### Using API Keys

```bash
curl https://api.devkit.io/v2/users \
  -H "Authorization: Bearer sk_live_abc123..."
```

### Key Types

| Prefix | Type | Environment |
|--------|------|-------------|
| sk_live_ | Secret key | Production |
| sk_test_ | Secret key | Sandbox |
| pk_live_ | Public key | Production (limited) |

## OAuth 2.0

### Authorization Flow

1. **Redirect user:**
```
https://devkit.io/oauth/authorize?
  client_id=YOUR_CLIENT_ID&
  redirect_uri=https://yourapp.com/callback&
  response_type=code&
  scope=read write
```

2. **Exchange code for token:**
```bash
POST https://api.devkit.io/oauth/token
Content-Type: application/json

{
  "grant_type": "authorization_code",
  "client_id": "YOUR_CLIENT_ID",
  "client_secret": "YOUR_CLIENT_SECRET",
  "code": "AUTH_CODE",
  "redirect_uri": "https://yourapp.com/callback"
}
```

3. **Use access token:**
```bash
Authorization: Bearer eyJhbGc...
```

### Scopes

| Scope | Access |
|-------|--------|
| read | Read all resources |
| write | Create and update |
| delete | Delete resources |
| admin | Full access |
| webhooks | Manage webhooks |

## Security Best Practices

- Never expose secret keys in client code
- Use environment variables for keys
- Rotate keys every 90 days
- Use minimum required scopes
- Implement key allowlisting by IP
```

---

### context/topics/errors.md

```markdown
# DevKit Error Reference

## Error Response Format

```json
{
  "errors": [
    {
      "code": "error_code",
      "message": "Human-readable message",
      "field": "field_name",
      "details": "Additional context"
    }
  ]
}
```

## HTTP Status Codes

| Code | Meaning | Action |
|------|---------|--------|
| 200 | Success | - |
| 201 | Created | - |
| 204 | No content | - |
| 400 | Bad request | Check request body |
| 401 | Unauthorized | Check authentication |
| 403 | Forbidden | Check permissions |
| 404 | Not found | Check resource ID |
| 409 | Conflict | Check for duplicates |
| 422 | Validation failed | Check field errors |
| 429 | Rate limited | Wait and retry |
| 500 | Server error | Contact support |

## Error Codes

### Authentication

| Code | Description |
|------|-------------|
| authentication_required | No auth header provided |
| invalid_token | Token is invalid or expired |
| insufficient_scope | Token lacks required scope |
| token_revoked | Token has been revoked |

### Validation

| Code | Description |
|------|-------------|
| validation_error | Field validation failed |
| missing_field | Required field not provided |
| invalid_format | Field format is wrong |
| value_too_long | Exceeds max length |

### Resources

| Code | Description |
|------|-------------|
| not_found | Resource doesn't exist |
| already_exists | Duplicate resource |
| resource_locked | Resource is being modified |
| deleted | Resource was deleted |

### Rate Limiting

| Code | Description |
|------|-------------|
| rate_limited | Too many requests |
| quota_exceeded | Monthly quota reached |

## Handling Errors

```javascript
try {
  const response = await devkit.users.create(userData);
} catch (error) {
  if (error.code === 'validation_error') {
    console.error('Field errors:', error.field);
  } else if (error.code === 'rate_limited') {
    await sleep(error.retryAfter);
    // Retry request
  }
}
```
```

---

## Actions File

### actions/index.json

```json
{
  "$schema": "https://pzp.riscent.com/schemas/actions.schema.json",
  "version": "1.0.0",
  "baseUrl": "https://pzp.docs.devkit.io",
  "actions": [
    {
      "name": "search-docs",
      "description": "Search all documentation content",
      "endpoint": "/api/search",
      "method": "GET",
      "input": {
        "query": {
          "type": "string",
          "required": true,
          "description": "Search query"
        },
        "section": {
          "type": "string",
          "enum": ["api-reference", "guides", "sdks", "all"],
          "default": "all",
          "description": "Limit to section"
        },
        "version": {
          "type": "string",
          "default": "2.0",
          "description": "API version"
        },
        "limit": {
          "type": "integer",
          "default": 10,
          "description": "Max results"
        }
      },
      "output": {
        "type": "application/json",
        "schema": {
          "results": "array of matching sections with snippets",
          "total": "total matches",
          "query": "original query"
        }
      },
      "cacheDuration": 300
    },
    {
      "name": "get-doc",
      "description": "Get a specific documentation page",
      "endpoint": "/data/docs/{path}",
      "method": "GET",
      "input": {
        "path": {
          "type": "string",
          "required": true,
          "description": "Document path (e.g., 'api-reference/users')"
        },
        "version": {
          "type": "string",
          "default": "2.0",
          "description": "API version"
        }
      },
      "output": {
        "type": "text/markdown"
      },
      "cacheDuration": 3600
    },
    {
      "name": "list-endpoints",
      "description": "List all API endpoints with summaries",
      "endpoint": "/data/docs/api-reference/index.json",
      "method": "GET",
      "input": {
        "category": {
          "type": "string",
          "enum": ["users", "projects", "tasks", "webhooks", "all"],
          "default": "all"
        }
      },
      "output": {
        "schema": {
          "endpoints": "array of endpoint definitions"
        }
      }
    },
    {
      "name": "get-examples",
      "description": "Get code examples for an endpoint",
      "endpoint": "/api/examples",
      "method": "GET",
      "input": {
        "endpoint": {
          "type": "string",
          "required": true,
          "description": "API endpoint path (e.g., '/users')"
        },
        "method": {
          "type": "string",
          "enum": ["GET", "POST", "PUT", "DELETE"],
          "required": true
        },
        "language": {
          "type": "string",
          "enum": ["curl", "javascript", "python", "go", "all"],
          "default": "all"
        }
      },
      "output": {
        "schema": {
          "examples": "code examples by language"
        }
      }
    },
    {
      "name": "list-docs",
      "description": "List all documentation pages",
      "endpoint": "/data/index.json",
      "method": "GET",
      "input": {
        "section": {
          "type": "string",
          "enum": ["api-reference", "guides", "sdks", "all"],
          "default": "all"
        }
      },
      "output": {
        "schema": {
          "pages": "array of page objects with paths and titles"
        }
      }
    },
    {
      "name": "get-changelog",
      "description": "Get recent API changes",
      "endpoint": "/data/changelog.json",
      "method": "GET",
      "input": {
        "version": {
          "type": "string",
          "description": "Filter by version"
        },
        "limit": {
          "type": "integer",
          "default": 10
        }
      },
      "output": {
        "schema": {
          "changes": "array of changelog entries"
        }
      }
    }
  ]
}
```

---

## Data Files

### data/index.json

```json
{
  "version": "2.0.0",
  "generated": "2026-03-17T00:00:00Z",
  "sections": [
    {
      "id": "getting-started",
      "title": "Getting Started",
      "pages": [
        {
          "path": "getting-started",
          "title": "Quick Start",
          "description": "Get up and running in 5 minutes",
          "tokens": 400
        },
        {
          "path": "installation",
          "title": "Installation",
          "description": "Install SDKs and set up your environment",
          "tokens": 350
        },
        {
          "path": "configuration",
          "title": "Configuration",
          "description": "Configure your DevKit integration",
          "tokens": 300
        },
        {
          "path": "authentication",
          "title": "Authentication",
          "description": "API keys and OAuth setup",
          "tokens": 500
        }
      ]
    },
    {
      "id": "api-reference",
      "title": "API Reference",
      "pages": [
        {
          "path": "api-reference/index",
          "title": "API Overview",
          "description": "API basics and conventions",
          "tokens": 600
        },
        {
          "path": "api-reference/users",
          "title": "Users API",
          "description": "Create, read, update, delete users",
          "tokens": 800
        },
        {
          "path": "api-reference/projects",
          "title": "Projects API",
          "description": "Project management endpoints",
          "tokens": 700
        },
        {
          "path": "api-reference/tasks",
          "title": "Tasks API",
          "description": "Task management endpoints",
          "tokens": 750
        },
        {
          "path": "api-reference/webhooks",
          "title": "Webhooks API",
          "description": "Event subscriptions and delivery",
          "tokens": 650
        }
      ]
    },
    {
      "id": "guides",
      "title": "Guides",
      "pages": [
        {
          "path": "guides/index",
          "title": "Guides Overview",
          "description": "How-to guides for common tasks",
          "tokens": 200
        },
        {
          "path": "guides/pagination",
          "title": "Pagination",
          "description": "Handling paginated responses",
          "tokens": 400
        },
        {
          "path": "guides/rate-limiting",
          "title": "Rate Limiting",
          "description": "Understanding and handling rate limits",
          "tokens": 350
        },
        {
          "path": "guides/error-handling",
          "title": "Error Handling",
          "description": "Best practices for error handling",
          "tokens": 450
        }
      ]
    },
    {
      "id": "sdks",
      "title": "SDKs",
      "pages": [
        {
          "path": "sdks/index",
          "title": "SDK Overview",
          "description": "Available SDKs and installation",
          "tokens": 300
        },
        {
          "path": "sdks/javascript",
          "title": "JavaScript SDK",
          "description": "Node.js and browser SDK",
          "tokens": 600
        },
        {
          "path": "sdks/python",
          "title": "Python SDK",
          "description": "Python SDK documentation",
          "tokens": 550
        },
        {
          "path": "sdks/go",
          "title": "Go SDK",
          "description": "Go SDK documentation",
          "tokens": 500
        }
      ]
    }
  ],
  "totalPages": 17,
  "totalTokens": 8350
}
```

---

### data/versions.json

```json
{
  "versions": [
    {
      "version": "2.0",
      "status": "current",
      "released": "2026-01-15",
      "basePath": "/v2/",
      "documentation": "/data/docs/",
      "changelog": "/data/changelog.json"
    },
    {
      "version": "1.5",
      "status": "supported",
      "released": "2025-06-01",
      "basePath": "/v1.5/",
      "documentation": "/data/docs/v1.5/",
      "deprecationDate": "2026-12-01",
      "migrationGuide": "/data/docs/guides/migrating-to-v2.md"
    },
    {
      "version": "1.0",
      "status": "deprecated",
      "released": "2024-01-01",
      "basePath": "/v1/",
      "sunsetDate": "2026-06-01",
      "notice": "v1.0 will be sunset on June 1, 2026. Please migrate to v2.0."
    }
  ],
  "default": "2.0",
  "latestStable": "2.0"
}
```

---

## Documentation Content Files

### data/docs/getting-started.md

```markdown
# Getting Started with DevKit

Get up and running with DevKit in 5 minutes.

## Prerequisites

- A DevKit account ([sign up free](https://devkit.io/signup))
- An API key ([get one here](https://devkit.io/settings/api))
- Node.js 18+, Python 3.8+, or Go 1.19+

## Quick Install

Choose your language:

**JavaScript:**
```bash
npm install @devkit/sdk
```

**Python:**
```bash
pip install devkit
```

**Go:**
```bash
go get github.com/devkit/devkit-go
```

## Your First API Call

**JavaScript:**
```javascript
import DevKit from '@devkit/sdk';

const devkit = new DevKit('sk_live_your_api_key');

const users = await devkit.users.list();
console.log(users);
```

**Python:**
```python
from devkit import DevKit

devkit = DevKit('sk_live_your_api_key')

users = devkit.users.list()
print(users)
```

**Go:**
```go
package main

import (
    "fmt"
    devkit "github.com/devkit/devkit-go"
)

func main() {
    client := devkit.NewClient("sk_live_your_api_key")
    users, _ := client.Users.List(nil)
    fmt.Println(users)
}
```

**cURL:**
```bash
curl https://api.devkit.io/v2/users \
  -H "Authorization: Bearer sk_live_your_api_key"
```

## Next Steps

- [Authentication](/data/docs/authentication.md) - Learn about API keys and OAuth
- [API Reference](/data/docs/api-reference/index.md) - Explore all endpoints
- [Guides](/data/docs/guides/index.md) - Common use cases and patterns
```

---

### data/docs/api-reference/users.md

```markdown
# Users API

The Users API lets you create and manage user accounts.

## Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | /users | List all users |
| POST | /users | Create a user |
| GET | /users/{id} | Get a user |
| PUT | /users/{id} | Update a user |
| DELETE | /users/{id} | Delete a user |

## List Users

```
GET /users
```

### Parameters

| Name | Type | Description |
|------|------|-------------|
| limit | integer | Items per page (default: 20, max: 100) |
| offset | integer | Number to skip |
| status | string | Filter by status: active, inactive, all |
| search | string | Search by name or email |

### Response

```json
{
  "data": [
    {
      "id": "usr_abc123",
      "email": "john@example.com",
      "name": "John Doe",
      "status": "active",
      "createdAt": "2026-01-15T10:00:00Z",
      "lastLoginAt": "2026-03-17T08:30:00Z"
    }
  ],
  "meta": {
    "total": 150,
    "limit": 20,
    "offset": 0,
    "hasMore": true
  }
}
```

### Example

```bash
curl "https://api.devkit.io/v2/users?limit=10&status=active" \
  -H "Authorization: Bearer sk_live_your_api_key"
```

## Create User

```
POST /users
```

### Body

```json
{
  "email": "jane@example.com",
  "name": "Jane Smith",
  "role": "member"
}
```

### Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| email | string | Yes | Unique email address |
| name | string | Yes | Full name |
| role | string | No | Role: admin, member (default: member) |
| metadata | object | No | Custom key-value data |

### Response

```json
{
  "data": {
    "id": "usr_def456",
    "email": "jane@example.com",
    "name": "Jane Smith",
    "role": "member",
    "status": "active",
    "createdAt": "2026-03-17T12:00:00Z"
  }
}
```

### Example

```bash
curl https://api.devkit.io/v2/users \
  -H "Authorization: Bearer sk_live_your_api_key" \
  -H "Content-Type: application/json" \
  -d '{"email":"jane@example.com","name":"Jane Smith"}'
```

## Get User

```
GET /users/{id}
```

### Response

```json
{
  "data": {
    "id": "usr_abc123",
    "email": "john@example.com",
    "name": "John Doe",
    "role": "admin",
    "status": "active",
    "createdAt": "2026-01-15T10:00:00Z",
    "lastLoginAt": "2026-03-17T08:30:00Z",
    "metadata": {}
  }
}
```

## Update User

```
PUT /users/{id}
```

### Body

Only include fields you want to update.

```json
{
  "name": "John Smith",
  "role": "admin"
}
```

## Delete User

```
DELETE /users/{id}
```

Returns 204 No Content on success.

## Error Codes

| Code | Description |
|------|-------------|
| user_not_found | User ID doesn't exist |
| email_exists | Email already registered |
| invalid_email | Email format is invalid |
| invalid_role | Role must be admin or member |
```

---

### data/docs/sdks/javascript.md

```markdown
# JavaScript SDK

The official DevKit SDK for Node.js and browsers.

## Installation

```bash
npm install @devkit/sdk
# or
yarn add @devkit/sdk
# or
pnpm add @devkit/sdk
```

## Quick Start

```javascript
import DevKit from '@devkit/sdk';

// Initialize with your API key
const devkit = new DevKit('sk_live_your_api_key');

// List users
const users = await devkit.users.list({ limit: 10 });

// Create a user
const newUser = await devkit.users.create({
  email: 'jane@example.com',
  name: 'Jane Smith'
});

// Get a user
const user = await devkit.users.get('usr_abc123');

// Update a user
const updated = await devkit.users.update('usr_abc123', {
  name: 'Jane Doe'
});

// Delete a user
await devkit.users.delete('usr_abc123');
```

## Configuration

```javascript
const devkit = new DevKit('sk_live_your_api_key', {
  // Use sandbox environment
  baseUrl: 'https://sandbox.api.devkit.io/v2',

  // Request timeout (ms)
  timeout: 30000,

  // Retry failed requests
  maxRetries: 3,

  // Custom HTTP agent
  agent: myHttpsAgent
});
```

## Resources

### Users

```javascript
// List with filters
const activeUsers = await devkit.users.list({
  status: 'active',
  limit: 50
});

// Search users
const results = await devkit.users.list({
  search: 'john'
});

// Create with metadata
const user = await devkit.users.create({
  email: 'jane@example.com',
  name: 'Jane Smith',
  metadata: {
    department: 'Engineering',
    startDate: '2026-03-01'
  }
});
```

### Projects

```javascript
// List projects
const projects = await devkit.projects.list();

// Create project
const project = await devkit.projects.create({
  name: 'New Project',
  description: 'Project description'
});

// Get project tasks
const tasks = await devkit.projects.tasks('prj_abc123').list();
```

### Tasks

```javascript
// Create task in project
const task = await devkit.projects.tasks('prj_abc123').create({
  title: 'Implement feature',
  assignee: 'usr_abc123',
  dueDate: '2026-04-01'
});

// Update task
await devkit.tasks.update('tsk_abc123', {
  status: 'completed'
});
```

### Webhooks

```javascript
// Create webhook
const webhook = await devkit.webhooks.create({
  url: 'https://yourapp.com/webhooks',
  events: ['user.created', 'task.completed']
});

// List webhooks
const webhooks = await devkit.webhooks.list();

// Delete webhook
await devkit.webhooks.delete('whk_abc123');
```

## Error Handling

```javascript
import DevKit, { DevKitError } from '@devkit/sdk';

try {
  const user = await devkit.users.create({
    email: 'invalid-email'
  });
} catch (error) {
  if (error instanceof DevKitError) {
    console.error('Code:', error.code);
    console.error('Message:', error.message);
    console.error('Field:', error.field);
    console.error('Status:', error.status);

    if (error.code === 'rate_limited') {
      console.log('Retry after:', error.retryAfter);
    }
  }
}
```

## Pagination

```javascript
// Manual pagination
let offset = 0;
const limit = 50;
let hasMore = true;

while (hasMore) {
  const response = await devkit.users.list({ limit, offset });
  console.log(response.data);

  hasMore = response.meta.hasMore;
  offset += limit;
}

// Auto-pagination with async iterator
for await (const user of devkit.users.listAll()) {
  console.log(user);
}
```

## TypeScript

The SDK includes TypeScript definitions:

```typescript
import DevKit, { User, Project, Task } from '@devkit/sdk';

const devkit = new DevKit('sk_live_your_api_key');

const user: User = await devkit.users.get('usr_abc123');
const projects: Project[] = (await devkit.projects.list()).data;
```

## Browser Usage

For browser environments, use the public key:

```javascript
import DevKit from '@devkit/sdk/browser';

const devkit = new DevKit('pk_live_your_public_key');

// Only read operations are allowed with public keys
const users = await devkit.users.list();
```
```

---

## Search Index

### data/search-index.json

```json
{
  "version": "2.0.0",
  "generated": "2026-03-17T00:00:00Z",
  "entries": [
    {
      "path": "/data/docs/getting-started.md",
      "title": "Getting Started",
      "section": "getting-started",
      "headings": ["Prerequisites", "Quick Install", "Your First API Call", "Next Steps"],
      "keywords": ["install", "setup", "quickstart", "first", "begin"],
      "content": "Get up and running with DevKit in 5 minutes. Prerequisites: DevKit account, API key..."
    },
    {
      "path": "/data/docs/authentication.md",
      "title": "Authentication",
      "section": "getting-started",
      "headings": ["API Keys", "OAuth 2.0", "Security Best Practices"],
      "keywords": ["auth", "api key", "oauth", "token", "bearer", "authorization"],
      "content": "API keys and OAuth 2.0 authentication methods for DevKit API..."
    },
    {
      "path": "/data/docs/api-reference/users.md",
      "title": "Users API",
      "section": "api-reference",
      "headings": ["List Users", "Create User", "Get User", "Update User", "Delete User"],
      "keywords": ["users", "create user", "list users", "delete user", "update user"],
      "content": "Create and manage user accounts with the Users API..."
    },
    {
      "path": "/data/docs/api-reference/projects.md",
      "title": "Projects API",
      "section": "api-reference",
      "headings": ["List Projects", "Create Project", "Get Project", "Update Project"],
      "keywords": ["projects", "create project", "list projects"],
      "content": "Project management endpoints for organizing work..."
    },
    {
      "path": "/data/docs/guides/pagination.md",
      "title": "Pagination Guide",
      "section": "guides",
      "headings": ["Offset Pagination", "Cursor Pagination", "Best Practices"],
      "keywords": ["pagination", "paging", "offset", "cursor", "limit", "next page"],
      "content": "How to handle paginated API responses..."
    },
    {
      "path": "/data/docs/sdks/javascript.md",
      "title": "JavaScript SDK",
      "section": "sdks",
      "headings": ["Installation", "Quick Start", "Configuration", "Resources", "Error Handling"],
      "keywords": ["javascript", "nodejs", "npm", "sdk", "client library"],
      "content": "Official DevKit SDK for Node.js and browsers..."
    }
  ],
  "totalEntries": 6
}
```

---

## Implementation Notes

### Build Process

Generate PZP documentation from your source docs:

```javascript
// scripts/generate-pzp.js
const fs = require('fs');
const path = require('path');
const matter = require('gray-matter');

function generatePZP() {
  const docsDir = './docs';
  const pzpDir = './pzp/data/docs';

  // Copy markdown files, stripping frontmatter
  const files = fs.readdirSync(docsDir, { recursive: true })
    .filter(f => f.endsWith('.md'));

  files.forEach(file => {
    const content = fs.readFileSync(path.join(docsDir, file), 'utf8');
    const { content: body } = matter(content);

    const outputPath = path.join(pzpDir, file);
    fs.mkdirSync(path.dirname(outputPath), { recursive: true });
    fs.writeFileSync(outputPath, body);
  });

  // Generate index
  const index = generateIndex(files);
  fs.writeFileSync('./pzp/data/index.json', JSON.stringify(index, null, 2));

  // Generate search index
  const searchIndex = generateSearchIndex(files);
  fs.writeFileSync('./pzp/data/search-index.json', JSON.stringify(searchIndex, null, 2));
}
```

### Search Implementation

Simple search endpoint:

```javascript
// api/search.js
app.get('/api/search', async (req, res) => {
  const { query, section, limit = 10 } = req.query;

  const searchIndex = require('./data/search-index.json');

  const results = searchIndex.entries
    .filter(entry => {
      if (section && section !== 'all' && entry.section !== section) {
        return false;
      }

      const searchText = `${entry.title} ${entry.content} ${entry.keywords.join(' ')}`.toLowerCase();
      return searchText.includes(query.toLowerCase());
    })
    .slice(0, parseInt(limit))
    .map(entry => ({
      path: entry.path,
      title: entry.title,
      section: entry.section,
      snippet: extractSnippet(entry.content, query)
    }));

  res.json({ results, total: results.length, query });
});
```

### Version Routing

Handle multiple documentation versions:

```javascript
// middleware/version.js
app.use('/data/docs/:version?/*', (req, res, next) => {
  const version = req.params.version || '2.0';
  const validVersions = ['2.0', '1.5', '1.0'];

  if (!validVersions.includes(version)) {
    return res.status(404).json({ error: 'version_not_found' });
  }

  req.docVersion = version;
  next();
});
```

---

*DevKit Documentation: API knowledge that AI agents can navigate.*
