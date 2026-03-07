# Prime Zero Protocol Essential Context

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
