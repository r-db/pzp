# Manifest Specification

> The site manifest defines identity, capabilities, and configuration.

## Overview

Every PZP site MUST have a `manifest.json` file at the root. The manifest is the first file agents read after discovery, providing essential metadata about the site.

**Location:** `/manifest.json`

**Content-Type:** `application/json; charset=utf-8`

---

## Schema

The manifest MUST validate against the PZP manifest schema:

```
$schema: https://pzp.riscent.com/schemas/manifest.schema.json
```

---

## Required Fields

### version

**Type:** `string` (semver)
**Required:** MUST

The site's content version. Agents use this to detect changes.

```json
{
  "version": "2.1.0"
}
```

**Rules:**
- MUST follow semantic versioning (MAJOR.MINOR.PATCH)
- SHOULD increment on content changes
- Agents MAY cache based on version

### protocolVersion

**Type:** `string` (semver)
**Required:** MUST

The PZP protocol version this site implements.

```json
{
  "protocolVersion": "1.0.0"
}
```

**Current version:** `1.0.0`

### name

**Type:** `string`
**Required:** MUST

Human-readable name of the site or service.

```json
{
  "name": "Acme Widget Store"
}
```

**Rules:**
- SHOULD be concise (under 50 characters)
- SHOULD match the organization or service name

### description

**Type:** `string`
**Required:** MUST

Brief description of what this site offers.

```json
{
  "description": "Professional and consumer-grade widgets with fast shipping"
}
```

**Rules:**
- SHOULD be one sentence
- SHOULD be under 150 characters
- MUST convey the site's primary purpose

### type

**Type:** `string` (enum)
**Required:** MUST

The primary classification of this site.

```json
{
  "type": "commerce"
}
```

**Valid values:**

| Type | Description | Typical Actions |
|------|-------------|-----------------|
| `service` | Executable actions/APIs | Execute, query |
| `documentation` | Reference material | Read, search |
| `data` | Dataset access | Query, download |
| `api` | Full CRUD API | All CRUD operations |
| `knowledge` | Knowledge base | Read, search |
| `commerce` | E-commerce | Browse, purchase |
| `content` | Blog, news, media | Read, subscribe |

### updated

**Type:** `string` (ISO 8601 datetime)
**Required:** MUST

When the site content was last updated.

```json
{
  "updated": "2026-03-06T14:30:00Z"
}
```

**Rules:**
- MUST be ISO 8601 format
- SHOULD include timezone (Z for UTC)
- Agents use this for cache invalidation

### endpoints

**Type:** `object`
**Required:** MUST

Paths to primary PZP directories.

```json
{
  "endpoints": {
    "context": "/context/",
    "actions": "/actions/",
    "data": "/data/",
    "meta": "/meta/"
  }
}
```

**Standard endpoints:**

| Endpoint | Path | Description |
|----------|------|-------------|
| `context` | `/context/` | Context files directory |
| `actions` | `/actions/` | Actions catalog |
| `data` | `/data/` | Data resources |
| `meta` | `/meta/` | Metadata and versioning |
| `schemas` | `/schemas/` | Custom schemas |

---

## Recommended Fields

### tokenBudgets

**Type:** `object`
**Required:** SHOULD

Declared token counts for context files.

```json
{
  "tokenBudgets": {
    "essential": 450,
    "full": 2000,
    "topic": 300
  }
}
```

**Purpose:** Helps agents plan context consumption without fetching files.

**Guidelines:**

| Context Type | Recommended Budget |
|--------------|-------------------|
| `essential` | 300-500 tokens |
| `full` | 1500-3000 tokens |
| `topic` | 200-500 tokens |

### capabilities

**Type:** `array` of `string`
**Required:** SHOULD

What agents can do on this site.

```json
{
  "capabilities": ["read", "search", "query", "transact"]
}
```

**Standard capabilities:**

| Capability | Description |
|------------|-------------|
| `read` | Read content |
| `search` | Search content |
| `query` | Query data with parameters |
| `write` | Create content |
| `update` | Modify content |
| `delete` | Remove content |
| `transact` | Execute transactions |
| `subscribe` | Subscribe to updates |

### authentication

**Type:** `object`
**Required:** SHOULD

Authentication requirements and methods.

```json
{
  "authentication": {
    "required": false,
    "publicEndpoints": ["/context/", "/data/categories.json"],
    "protectedEndpoints": ["/api/cart/", "/api/checkout"],
    "methods": ["api-key", "oauth2", "session"]
  }
}
```

**Fields:**

| Field | Type | Description |
|-------|------|-------------|
| `required` | boolean | Default auth requirement |
| `publicEndpoints` | array | Paths accessible without auth |
| `protectedEndpoints` | array | Paths requiring auth |
| `methods` | array | Supported auth methods |

**Auth methods:**

| Method | Description |
|--------|-------------|
| `api-key` | API key in header |
| `oauth2` | OAuth 2.0 flow |
| `session` | Session-based auth |
| `bearer` | Bearer token |
| `basic` | Basic HTTP auth |

---

## Optional Fields

### rateLimits

**Type:** `object`
**Required:** MAY

Rate limiting configuration.

```json
{
  "rateLimits": {
    "default": {
      "requests": 100,
      "window": "1m"
    },
    "search": {
      "requests": 50,
      "window": "1m"
    }
  }
}
```

**Window formats:** `1s`, `1m`, `1h`, `1d`

### contact

**Type:** `object`
**Required:** MAY

Contact information for the site.

```json
{
  "contact": {
    "support": "support@example.com",
    "api": "api@example.com",
    "website": "https://example.com/contact"
  }
}
```

### owner

**Type:** `object`
**Required:** MAY

Organization that owns this site.

```json
{
  "owner": {
    "name": "Acme Corporation",
    "url": "https://acme.com",
    "type": "organization"
  }
}
```

### language

**Type:** `string` or `array`
**Required:** MAY

Primary language(s) of content.

```json
{
  "language": "en"
}
```

Or multiple:

```json
{
  "language": ["en", "es", "fr"]
}
```

**Format:** ISO 639-1 language codes

### region

**Type:** `string` or `array`
**Required:** MAY

Geographic regions served.

```json
{
  "region": ["US", "CA", "GB"]
}
```

**Format:** ISO 3166-1 alpha-2 country codes

### tags

**Type:** `array` of `string`
**Required:** MAY

Keywords describing the site.

```json
{
  "tags": ["widgets", "hardware", "professional", "ecommerce"]
}
```

---

## Complete Example

```json
{
  "$schema": "https://pzp.riscent.com/schemas/manifest.schema.json",
  "version": "2.1.0",
  "protocolVersion": "1.0.0",
  "name": "Acme Widget Store",
  "description": "Professional and consumer-grade widgets with fast shipping to US and Canada",
  "type": "commerce",
  "updated": "2026-03-06T14:30:00Z",

  "tokenBudgets": {
    "essential": 450,
    "full": 2000,
    "productSummary": 100,
    "productFull": 250
  },

  "endpoints": {
    "context": "/context/",
    "actions": "/actions/",
    "data": "/data/",
    "meta": "/meta/"
  },

  "capabilities": [
    "read",
    "search",
    "query",
    "transact"
  ],

  "authentication": {
    "required": false,
    "publicEndpoints": [
      "/context/",
      "/data/",
      "/api/products/"
    ],
    "protectedEndpoints": [
      "/api/cart/",
      "/api/checkout",
      "/api/orders/"
    ],
    "methods": ["api-key", "oauth2", "session"]
  },

  "rateLimits": {
    "default": {
      "requests": 100,
      "window": "1m"
    },
    "search": {
      "requests": 50,
      "window": "1m"
    },
    "checkout": {
      "requests": 10,
      "window": "1m"
    }
  },

  "contact": {
    "support": "support@acmewidgets.com",
    "api": "api@acmewidgets.com"
  },

  "owner": {
    "name": "Acme Corporation",
    "url": "https://acme.com",
    "type": "organization"
  },

  "language": "en",
  "region": ["US", "CA"],
  "tags": ["widgets", "hardware", "professional", "ecommerce"]
}
```

---

## Validation

### Required Field Check

```
✓ version exists and is semver
✓ protocolVersion exists and is semver
✓ name exists and is string
✓ description exists and is string
✓ type exists and is valid enum
✓ updated exists and is ISO 8601
✓ endpoints exists and has at least one entry
```

### Schema Validation

Validate against the manifest schema:

```bash
# Using ajv-cli
ajv validate -s manifest.schema.json -d manifest.json
```

### Common Errors

| Error | Cause | Fix |
|-------|-------|-----|
| Missing `$schema` | No schema reference | Add schema URL |
| Invalid `version` | Not semver format | Use X.Y.Z format |
| Invalid `updated` | Wrong date format | Use ISO 8601 |
| Unknown `type` | Non-standard type | Use defined types |

---

## Agent Behavior

### Reading Manifest

1. Fetch `/manifest.json`
2. Validate JSON structure
3. Check `protocolVersion` compatibility
4. Extract `endpoints` for navigation
5. Check `capabilities` for available actions
6. Respect `rateLimits` if present

### Using Token Budgets

```python
# Agent logic example
manifest = fetch("/manifest.json")
budgets = manifest.get("tokenBudgets", {})

# Decide what to read based on available context
if remaining_tokens > budgets.get("full", 2000):
    read("/context/full.md")
elif remaining_tokens > budgets.get("essential", 500):
    read("/context/essential.md")
else:
    # Token budget too tight, use manifest description only
    use(manifest["description"])
```

### Caching

Agents SHOULD cache manifests based on:
- `Cache-Control` response header
- `updated` field changes
- `version` field changes

---

## Versioning

### Site Version

The `version` field tracks content changes:

- **MAJOR:** Breaking changes to structure or actions
- **MINOR:** New content or capabilities
- **PATCH:** Content corrections, minor updates

### Protocol Version

The `protocolVersion` field indicates PZP compatibility:

- `1.0.0` - Current specification
- Future versions will maintain backward compatibility within major versions

---

*The manifest is the handshake. Make it complete.*
