# Prime Zero Protocol

**The machine-native web.**

Websites built for synthetic intelligence. No UI. No CSS. No compromise.

## What is PZP?

Prime Zero Protocol (PZP) is a standard for creating websites designed exclusively for AI agent consumption. PZP sites exist as a parallel layer to human-facing websites—pure, token-efficient, machine-readable content.

## Why PZP?

**The Problem:**
Every day, AI agents waste billions of tokens parsing HTML designed for human eyes. They extract content from pages cluttered with navigation, ads, and styling. They fail when JavaScript doesn't execute.

**The Solution:**
A separate web layer designed for machines:
- Zero parsing overhead (no HTML/CSS/JS)
- Predictable file structure (always know where to look)
- Token-efficient content (maximum information density)
- Self-describing schemas (machines understand machines)

## Quick Start

```bash
# 1. Create structure
mkdir -p pzp-site/{context,actions,data,meta}

# 2. Copy templates
cp templates/*.template.* pzp-site/

# 3. Customize files (replace {{PLACEHOLDERS}})

# 4. Deploy to pzp.yourdomain.com
```

## Documentation

| Document | Description |
|----------|-------------|
| [spec/v1.md](spec/v1.md) | Full specification |
| [human/](human/) | Marketing site |
| [templates/](templates/) | Ready-to-use templates |
| [schemas/](schemas/) | JSON Schema definitions |

## Directory Structure

```
pzp/
├── spec/              # Protocol specification
│   └── v1.md
├── schemas/           # JSON Schema definitions
│   ├── manifest.schema.json
│   ├── actions.schema.json
│   ├── data.schema.json
│   └── version.schema.json
├── templates/         # File templates
├── human/             # Human-facing marketing site
├── context/           # PZP's own context (meta)
├── actions/           # PZP's own actions
├── data/              # Protocol data
└── meta/              # Metadata
```

## File Requirements

**Required:**
- `index.md` - Entry point
- `manifest.json` - Site metadata
- `context/essential.md` - Minimum context
- `robots.txt` - Agent discovery

**Recommended:**
- `context/full.md` - Complete context
- `actions/index.json` - Available actions
- `data/schema.json` - Data schemas
- `meta/version.json` - Version info

## Discovery

Three methods for agents to find your PZP site:

1. **robots.txt** - `X-PZP-Site: true`
2. **DNS TXT record** - `_pzp.domain TXT "v=pzp1..."`
3. **HTML link tag** - `<link rel="pzp-site">`

## License

CC0 1.0 Universal - Public Domain

---

*Built for synthetic intelligence, by synthetic intelligence.*

*© 2026 Riscent*
