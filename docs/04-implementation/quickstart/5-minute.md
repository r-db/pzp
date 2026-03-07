# 5-Minute PZP Setup

> From zero to machine-native in 5 minutes.

## Prerequisites

- GitHub account
- Vercel account (free tier works)

---

## Step 1: Create Repository (1 minute)

Create a new directory with these 4 files:

### index.md

```markdown
# My PZP Site

> A minimal PZP implementation.

## Identity

- **Type:** documentation
- **Version:** 1.0.0

## Quick Links

- Context: [/context/essential.md](context/essential.md)
- Manifest: [/manifest.json](manifest.json)
```

### manifest.json

```json
{
  "$schema": "https://pzp.riscent.com/schemas/manifest.schema.json",
  "version": "1.0.0",
  "protocolVersion": "1.0.0",
  "name": "My PZP Site",
  "description": "A minimal PZP implementation",
  "type": "documentation",
  "updated": "2026-03-06T00:00:00Z",
  "tokenBudgets": {
    "essential": 300,
    "full": 1000
  },
  "endpoints": {
    "context": "/context/"
  },
  "capabilities": ["read"],
  "authentication": {
    "required": false
  }
}
```

### robots.txt

```
User-agent: *
Allow: /

X-PZP-Site: true
X-PZP-Manifest: /manifest.json
X-PZP-Version: 1.0.0
```

### context/essential.md

```markdown
# Essential Context

## What This Is

This is a minimal PZP site demonstrating the protocol basics.

## Capabilities

- Read documentation
- Explore file structure

## Files

| Path | Description |
|------|-------------|
| /index.md | Entry point |
| /manifest.json | Site manifest |
| /context/essential.md | This file |
```

---

## Step 2: Push to GitHub (1 minute)

```bash
git init
git add .
git commit -m "Initial PZP site"
gh repo create my-pzp-site --public --source=. --push
```

---

## Step 3: Deploy to Vercel (2 minutes)

1. Go to [vercel.com/new](https://vercel.com/new)
2. Import your GitHub repository
3. Click **Deploy**
4. Wait for deployment (usually 30 seconds)

---

## Step 4: Add Custom Domain (1 minute)

In Vercel dashboard:

1. Go to your project → Settings → Domains
2. Add `pzp.yourdomain.com`
3. Follow DNS instructions

In Cloudflare (or your DNS provider):
- Add CNAME: `pzp` → `cname.vercel-dns.com`

---

## Step 5: Verify (30 seconds)

```bash
# Test manifest
curl -s https://pzp.yourdomain.com/manifest.json | head -5

# Test headers
curl -sI https://pzp.yourdomain.com/ | grep -i pzp
# Should show: X-PZP-Site: true
```

---

## You're Live!

Your PZP site is now accessible to AI agents at:

```
https://pzp.yourdomain.com/
```

---

## What You've Created

```
pzp.yourdomain.com/
├── index.md          ✓ Entry point
├── manifest.json     ✓ Site metadata
├── robots.txt        ✓ Discovery headers
└── context/
    └── essential.md  ✓ Basic context
```

This minimal setup satisfies PZP **Minimal Conformance**.

---

## Next: Add More Depth

### Add Full Context

Create `context/full.md`:

```markdown
# Full Context

## Overview

[Detailed description of your site - 500-1000 words]

## Detailed Capabilities

### Reading Documentation
[Explain what documentation is available]

### Navigation
[How to navigate the site]

## Common Use Cases

1. [Use case 1]
2. [Use case 2]
3. [Use case 3]

## Constraints

- [Constraint 1]
- [Constraint 2]
```

### Add Actions

Create `actions/index.json`:

```json
{
  "$schema": "https://pzp.riscent.com/schemas/actions.schema.json",
  "version": "1.0.0",
  "description": "Available actions",
  "baseUrl": "https://pzp.yourdomain.com",
  "actions": [
    {
      "name": "list-files",
      "description": "List all available files",
      "endpoint": "/sitemap.txt",
      "method": "GET",
      "input": {},
      "output": {
        "type": "text/plain",
        "description": "Newline-separated list of file paths"
      }
    }
  ]
}
```

Update `manifest.json`:

```json
{
  "endpoints": {
    "context": "/context/",
    "actions": "/actions/"
  }
}
```

---

## Troubleshooting

### "X-PZP-Site header not showing"

Add to `vercel.json`:

```json
{
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        { "key": "X-PZP-Site", "value": "true" },
        { "key": "X-PZP-Version", "value": "1.0.0" }
      ]
    }
  ]
}
```

### "Markdown not rendering as text/markdown"

Add to `vercel.json`:

```json
{
  "headers": [
    {
      "source": "/:path*.md",
      "headers": [
        { "key": "Content-Type", "value": "text/markdown; charset=utf-8" }
      ]
    }
  ]
}
```

### "DNS not propagating"

DNS changes can take up to 48 hours. Check with:

```bash
dig pzp.yourdomain.com CNAME +short
```

---

## Quick Reference

| File | Purpose | Required |
|------|---------|----------|
| `index.md` | Entry point | MUST |
| `manifest.json` | Site metadata | MUST |
| `robots.txt` | Discovery | MUST |
| `context/essential.md` | Quick context | SHOULD |
| `context/full.md` | Full context | SHOULD |
| `actions/index.json` | Actions catalog | MAY |

---

*5 minutes. Machine-native. Done.*
