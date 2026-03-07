# Protocol Architecture

> The three-layer architecture of Prime Zero Protocol.

## Overview

Prime Zero Protocol is organized into three distinct layers, each serving a specific purpose in the agent consumption flow:

```
┌─────────────────────────────────────────────────────────────┐
│                     LAYER 3: CONSUMPTION                    │
│                                                             │
│   Content Retrieval  │  Action Execution  │  Data Access   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                     LAYER 2: STRUCTURE                      │
│                                                             │
│   File Organization  │  Schema Validation  │  Versioning   │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                     LAYER 1: DISCOVERY                      │
│                                                             │
│   robots.txt Headers │  DNS TXT Records  │  HTML Link Tags │
└─────────────────────────────────────────────────────────────┘
```

---

## Layer 1: Discovery

### Purpose

Enable agents to identify PZP-compliant sites and locate PZP endpoints without prior knowledge.

### Mechanisms

#### 1.1 robots.txt Headers

The robots.txt file MUST include PZP discovery headers:

```
# Standard robots.txt content
User-agent: *
Allow: /

# PZP Discovery Headers
X-PZP-Site: true
X-PZP-Manifest: /manifest.json
X-PZP-Version: 1.0.0
```

**Rationale:** Agents already check robots.txt. Including PZP headers here requires no additional requests.

#### 1.2 DNS TXT Records

Sites MAY publish a DNS TXT record for discovery:

```
_pzp.example.com.  IN  TXT  "v=pzp1 endpoint=pzp.example.com type=documentation"
```

**Record format:**
| Field | Required | Description |
|-------|----------|-------------|
| `v` | MUST | Protocol version (`pzp1`) |
| `endpoint` | MUST | PZP site hostname |
| `type` | SHOULD | Site type |

**Use case:** Allows discovery from the main domain without hitting the PZP subdomain.

#### 1.3 HTML Link Elements

Human-facing sites MAY include a link to their PZP counterpart:

```html
<link rel="pzp-site" href="https://pzp.example.com/">
```

**Use case:** Agents parsing the human site can discover the PZP alternative.

### Discovery Flow

```
Agent wants to interact with example.com

1. Check DNS TXT record: _pzp.example.com
   ├─ Found? → Use endpoint from record
   └─ Not found? → Continue

2. Fetch robots.txt from example.com
   ├─ Has X-PZP-Site header? → PZP is at example.com
   └─ No header? → Continue

3. Check for subdomain pzp.example.com
   ├─ Fetch robots.txt from pzp.example.com
   └─ Has X-PZP-Site header? → PZP found

4. If human page fetched, check for <link rel="pzp-site">
   └─ Found? → Follow href

5. No PZP site available
```

---

## Layer 2: Structure

### Purpose

Provide predictable organization of content so agents always know where to find what they need.

### File Organization

#### 2.1 Root Level (Required)

Every PZP site MUST have these files at the root:

```
pzp.example.com/
├── index.md              # Entry point
├── manifest.json         # Site manifest
└── robots.txt            # Discovery (with headers)
```

#### 2.2 Context Directory (Recommended)

PZP sites SHOULD have a context directory:

```
pzp.example.com/
└── context/
    ├── essential.md      # ~500 tokens
    └── full.md           # ~2000-3000 tokens
```

#### 2.3 Actions Directory (Recommended)

PZP sites with executable actions SHOULD have:

```
pzp.example.com/
└── actions/
    └── index.json        # Action catalog
```

#### 2.4 Optional Directories

```
pzp.example.com/
├── data/                 # Data resources
│   ├── index.json        # Data catalog
│   └── {resource}.json   # Individual datasets
├── meta/                 # Site metadata
│   ├── version.json      # Version info
│   └── changelog.md      # Change history
├── schemas/              # Custom schemas
│   └── {name}.schema.json
└── context/
    └── topics/           # Topic-specific context
        └── {topic}.md
```

### Schema Validation

All JSON files MUST validate against their respective schemas:

| File | Schema |
|------|--------|
| `manifest.json` | `manifest.schema.json` |
| `actions/index.json` | `actions.schema.json` |
| `data/index.json` | `data.schema.json` |
| `meta/version.json` | `version.schema.json` |

Schemas are available at `https://pzp.riscent.com/schemas/`.

### Versioning

#### Protocol Version

The PZP protocol version follows [Semantic Versioning](https://semver.org/):
- **Major:** Breaking changes to file structure or required fields
- **Minor:** New optional features
- **Patch:** Bug fixes and clarifications

Current version: `1.0.0`

#### Site Version

Each PZP site has its own version, declared in `manifest.json`:

```json
{
  "version": "2.1.0",
  "protocolVersion": "1.0.0"
}
```

---

## Layer 3: Consumption

### Purpose

Define how agents retrieve content, execute actions, and access data.

### Content Retrieval

#### 3.1 Standard Flow

```
1. Read manifest.json
   └─ Understand site type, capabilities, token budgets

2. Read context/essential.md
   └─ Determine relevance to current task

3. If relevant:
   ├─ Read context/full.md for complete understanding
   └─ Read topic-specific context if available

4. Navigate to specific content as needed
   └─ Use file paths from manifest endpoints
```

#### 3.2 Efficient Retrieval

Agents SHOULD minimize requests:

| Need | Fetch |
|------|-------|
| Quick relevance check | `manifest.json` only |
| Basic understanding | + `context/essential.md` |
| Full context | + `context/full.md` |
| Specific action | + `actions/index.json` |

#### 3.3 Response Headers

PZP sites MUST return these headers with every response:

```http
HTTP/1.1 200 OK
Content-Type: text/markdown; charset=utf-8
X-PZP-Site: true
X-PZP-Version: 1.0.0
X-PZP-Tokens: 450
Cache-Control: public, max-age=3600
```

| Header | Required | Description |
|--------|----------|-------------|
| `X-PZP-Site` | MUST | Confirms PZP compliance |
| `X-PZP-Version` | MUST | Protocol version |
| `X-PZP-Tokens` | SHOULD | Token count of response |
| `Cache-Control` | SHOULD | Caching directive |

### Action Execution

#### 3.4 Action Discovery

Actions are defined in `actions/index.json`:

```json
{
  "actions": [
    {
      "name": "search-products",
      "description": "Search product catalog",
      "endpoint": "/api/search",
      "method": "GET",
      "input": {
        "query": {
          "type": "string",
          "required": true
        }
      }
    }
  ]
}
```

#### 3.5 Action Execution Flow

```
1. Agent reads actions/index.json
2. Agent identifies relevant action
3. Agent constructs request from schema
4. Agent executes request to endpoint
5. Agent processes structured response
```

#### 3.6 Action Response

Action endpoints MUST return structured JSON:

```json
{
  "success": true,
  "data": { ... },
  "meta": {
    "tokens": 150,
    "cached": false
  }
}
```

### Data Access

#### 3.7 Data Discovery

Data resources are cataloged in `data/index.json`:

```json
{
  "resources": [
    {
      "name": "products",
      "path": "/data/products.json",
      "description": "Complete product catalog",
      "format": "json",
      "tokens": 5000
    }
  ]
}
```

#### 3.8 Data Retrieval

Agents can fetch data resources directly by path:

```
GET /data/products.json HTTP/1.1
Host: pzp.example.com
Accept: application/json
```

---

## Component Relationships

```
┌─────────────────────────────────────────────────────────────┐
│                        manifest.json                        │
│                                                             │
│  Declares:                                                  │
│  - Site identity and type                                   │
│  - Endpoint locations                                       │
│  - Token budgets                                            │
│  - Capabilities                                             │
└──────────────────────────┬──────────────────────────────────┘
                           │
           ┌───────────────┼───────────────┐
           │               │               │
           ▼               ▼               ▼
    ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
    │  context/   │ │  actions/   │ │   data/     │
    │             │ │             │ │             │
    │ essential.md│ │ index.json  │ │ index.json  │
    │ full.md     │ │             │ │ {files}     │
    │ topics/     │ │             │ │             │
    └─────────────┘ └─────────────┘ └─────────────┘
          │               │               │
          │               │               │
          └───────────────┴───────────────┘
                          │
                          ▼
                  Agent understands:
                  - What this site is
                  - What it can do
                  - What data is available
                  - How to interact
```

---

## Security Architecture

### Authentication

PZP sites MAY require authentication for some or all endpoints:

```json
{
  "authentication": {
    "required": false,
    "publicEndpoints": ["/context/", "/manifest.json"],
    "methods": ["api-key", "oauth2"]
  }
}
```

### Rate Limiting

PZP sites SHOULD implement rate limiting:

```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1677721600
```

### CORS

PZP sites MUST allow cross-origin requests:

```http
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, POST, OPTIONS
Access-Control-Allow-Headers: Content-Type, Authorization
```

---

## Design Principles

### 1. Predictability Over Flexibility

Agents benefit from knowing exactly where to find content. PZP prioritizes standard paths over configuration:

**Good:** `/context/essential.md` always exists at the same path
**Bad:** Context file location varies per site

### 2. Efficiency Over Completeness

Agents have token budgets. PZP prioritizes information density:

**Good:** Essential context in 500 tokens
**Bad:** Essential context with verbose explanations

### 3. Structure Over Inference

Agents shouldn't guess. PZP makes everything explicit:

**Good:** Actions defined in JSON schema
**Bad:** Actions inferred from HTML forms

### 4. Parallel Over Replacement

PZP complements human-facing sites, doesn't replace them:

**Good:** `pzp.example.com` alongside `example.com`
**Bad:** `example.com` tries to serve both humans and machines

---

## Implementation Considerations

### Subdomain Strategy

The recommended pattern is a dedicated PZP subdomain:

```
example.com          # Human-facing site
pzp.example.com      # PZP site
```

**Advantages:**
- Clear separation of concerns
- Independent deployment
- Different caching rules
- No interference with human site

### Same-Domain Strategy

PZP MAY be implemented on the same domain:

```
example.com/          # Human-facing site
example.com/pzp/      # PZP endpoints
```

**Considerations:**
- Requires careful routing
- Mixed content types
- Discovery must specify path

### Static vs. Dynamic

PZP sites MAY be:
- **Static:** Pre-generated files served directly
- **Dynamic:** Generated on request from database/CMS
- **Hybrid:** Static structure with dynamic data endpoints

Static is preferred for content that doesn't change frequently.

---

*Architecture is frozen music. Protocol architecture is frozen efficiency.*
