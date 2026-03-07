# Prime Zero Protocol Complete Context

## What This Is

Prime Zero Protocol (PZP) is a standard for creating websites optimized for synthetic intelligence consumption. PZP sites exist parallel to human sites—no HTML, no CSS, no JavaScript. Pure markdown and JSON. Maximum token efficiency.

## Key Capabilities

- Access complete protocol specification
- Retrieve JSON schemas for validation
- Download file templates
- Validate manifests and actions

## Primary Actions

| Action | Endpoint | Method | Description |
|--------|----------|--------|-------------|
| get-schema | /api/schema/{name} | GET | Retrieve schema by name |
| validate | /api/validate | POST | Validate JSON against schema |

## File Structure

```
pzp.example.com/
├── index.md           # Entry point
├── manifest.json      # Site metadata
├── context/
│   ├── essential.md   # ~500 tokens
│   └── full.md        # Complete docs
├── actions/
│   └── index.json     # Action catalog
└── meta/
    └── version.json   # Version info
```

## Constraints

- Read-only (no write operations)
- Rate limited: 100 requests/minute
- JSON and Markdown responses only
- No authentication required

---

## Protocol Specification

### Required Files

**index.md** - Entry point
- H1 with site name
- Blockquote with description
- Identity block (domain, type, version, updated)
- Purpose statement
- Navigation table

**manifest.json** - Site metadata
- version: Semantic version
- name: Site name
- description: One-line description
- type: service|documentation|data|api|knowledge
- updated: ISO 8601 timestamp
- endpoints: Path mappings

**context/essential.md** - Minimum context (~500 tokens)
- What This Is (single paragraph)
- Key Capabilities (3-5 bullets)
- Primary Actions (table)
- Constraints (bullets)

### Response Headers

```
Content-Type: text/markdown; charset=utf-8
X-Token-Count: {tokens}
X-Content-Version: {version}
X-Last-Modified: {ISO 8601}
X-PZP-Version: 1.0.0
```

### Discovery Methods

1. **robots.txt**
   ```
   X-PZP-Site: true
   X-PZP-Manifest: /manifest.json
   ```

2. **DNS TXT**
   ```
   _pzp.domain TXT "v=pzp1 endpoint=pzp.domain type=service"
   ```

3. **HTML Link**
   ```
   <link rel="pzp-site" href="https://pzp.domain/">
   ```

## Schemas

| Schema | Description |
|--------|-------------|
| manifest.schema.json | Site manifest validation |
| actions.schema.json | Action catalog validation |
| data.schema.json | Data schema registry |
| version.schema.json | Version/health info |

## Site Types

| Type | Use When |
|------|----------|
| service | Executable actions/APIs |
| documentation | Reference material |
| data | Dataset access |
| api | Full CRUD API |
| knowledge | Knowledge base |

## Token Budgets

| Context | Target |
|---------|--------|
| essential | 300-500 |
| full | 1500-3000 |
| topic-specific | 200-500 each |

## Action Definition

```json
{
  "name": "action-name",
  "description": "What it does",
  "endpoint": "/api/path",
  "method": "POST",
  "input": { "param": { "type": "string", "required": true } },
  "output": { "type": "object" },
  "errors": { "400": "Invalid request" }
}
```

---

*Built for synthetic intelligence, by synthetic intelligence.*
*© 2026 Riscent*
