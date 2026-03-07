# Quick Start Guide

> Get your PZP site live in minutes.

## Overview

This guide covers three paths to your first PZP site:

| Guide | Time | Best For |
|-------|------|----------|
| [5-Minute Setup](5-minute.md) | 5 min | Trying PZP, minimal sites |
| [Static Site](static-site.md) | 30 min | Blogs, docs, portfolios |
| [Dynamic Site](dynamic-site.md) | 1 hour | E-commerce, SaaS, APIs |

---

## Before You Start

### What You Need

- A domain you control
- Access to your DNS settings
- A hosting platform (Vercel, Netlify, Cloudflare, etc.)
- Basic understanding of Markdown and JSON

### What You'll Create

```
pzp.yourdomain.com/
├── index.md              # Entry point
├── manifest.json         # Site metadata
├── robots.txt            # Discovery
├── context/
│   ├── essential.md      # Quick context (~500 tokens)
│   └── full.md           # Full context (~2000 tokens)
└── actions/
    └── index.json        # What agents can do
```

---

## The Fastest Path

### 1. Create Your Files

**index.md:**
```markdown
# Your Site Name

> One line describing what this site does.

## Identity

- **Domain:** yourdomain.com
- **Type:** [documentation|commerce|service|api]
- **Version:** 1.0.0

## What This Site Does

Brief description of your site's purpose and what agents can do here.

## Quick Start

1. Read context: [/context/essential.md](context/essential.md)
2. See actions: [/actions/index.json](actions/index.json)
```

**manifest.json:**
```json
{
  "$schema": "https://pzp.riscent.com/schemas/manifest.schema.json",
  "version": "1.0.0",
  "protocolVersion": "1.0.0",
  "name": "Your Site Name",
  "description": "What your site does in one sentence",
  "type": "documentation",
  "updated": "2026-03-06T00:00:00Z",
  "tokenBudgets": {
    "essential": 500,
    "full": 2000
  },
  "endpoints": {
    "context": "/context/",
    "actions": "/actions/"
  }
}
```

**robots.txt:**
```
User-agent: *
Allow: /

X-PZP-Site: true
X-PZP-Manifest: /manifest.json
X-PZP-Version: 1.0.0
```

**context/essential.md:**
```markdown
# [Your Site] Essential Context

## What This Is

[2-3 sentences about what your site/business does]

## Key Capabilities

- [Capability 1]
- [Capability 2]
- [Capability 3]

## Primary Actions

| Action | Description |
|--------|-------------|
| [action-1] | [What it does] |
| [action-2] | [What it does] |
```

### 2. Deploy

Choose your platform:

**Vercel (Recommended):**
```bash
cd your-pzp-site
vercel
vercel --prod
```

**Netlify:**
```bash
netlify deploy --prod
```

**Cloudflare Pages:**
```bash
wrangler pages deploy .
```

### 3. Configure DNS

Add a CNAME record:
- **Name:** `pzp`
- **Target:** Your deployment URL (e.g., `your-site.vercel.app`)

### 4. Verify

```bash
# Check manifest
curl https://pzp.yourdomain.com/manifest.json

# Check headers
curl -I https://pzp.yourdomain.com/

# Should see: X-PZP-Site: true
```

---

## Next Steps

| If you want to... | Read |
|-------------------|------|
| Add more context depth | [Context Specification](/docs/02-specification/context.md) |
| Define actions | [Actions Specification](/docs/02-specification/actions.md) |
| See real examples | [Use Cases](/docs/03-use-cases/) |
| Use a framework | [Framework Guides](/docs/04-implementation/frameworks/) |

---

## Common Questions

### Do I need a separate subdomain?

Recommended but not required. Subdomain keeps PZP separate from your human site.

### Can I add PZP to my existing site?

Yes. You can add PZP files to a `/pzp/` path on your existing domain.

### What if my content changes frequently?

Use the `updated` field in manifest.json and appropriate Cache-Control headers.

### How do agents find my PZP site?

Through [discovery mechanisms](/docs/02-specification/discovery.md): robots.txt headers, DNS TXT records, or HTML link elements.

---

*Five minutes from nothing to machine-native. Start now.*
