# Discovery Specification

> How agents find and identify PZP sites.

## Overview

Discovery is the process by which an agent identifies that a domain implements PZP and locates the PZP endpoint. PZP defines three discovery mechanisms that SHOULD be implemented:

1. **robots.txt headers** - Primary mechanism (MUST)
2. **DNS TXT records** - Secondary mechanism (SHOULD)
3. **HTML link elements** - Tertiary mechanism (MAY)

Agents SHOULD check these mechanisms in order, stopping when PZP is found.

---

## 1. robots.txt Discovery

### Requirements

Every PZP site MUST include discovery headers in its robots.txt file.

### Format

```
# robots.txt for pzp.example.com

User-agent: *
Allow: /

# PZP Discovery Headers
X-PZP-Site: true
X-PZP-Manifest: /manifest.json
X-PZP-Version: 1.0.0
```

### Required Headers

| Header | Value | Description |
|--------|-------|-------------|
| `X-PZP-Site` | `true` | MUST be present to indicate PZP compliance |
| `X-PZP-Manifest` | Path to manifest | MUST specify manifest location |
| `X-PZP-Version` | Semantic version | MUST specify protocol version |

### Optional Headers

| Header | Value | Description |
|--------|-------|-------------|
| `X-PZP-Type` | Site type | MAY specify site type |
| `X-PZP-Contact` | Email/URL | MAY specify contact |

### Example: Full robots.txt

```
# Prime Zero Protocol Site
# The machine-native web

User-agent: *
Allow: /

# AI Agent Allowlist
User-agent: GPTBot
Allow: /

User-agent: ChatGPT-User
Allow: /

User-agent: ClaudeBot
Allow: /

User-agent: Claude-User
Allow: /

User-agent: PerplexityBot
Allow: /

User-agent: Google-Extended
Allow: /

# PZP Discovery
X-PZP-Site: true
X-PZP-Manifest: /manifest.json
X-PZP-Version: 1.0.0
X-PZP-Type: documentation

# Sitemap
Sitemap: /sitemap.txt
```

### Agent Behavior

When an agent fetches robots.txt:

1. Parse standard robots.txt directives
2. Check for `X-PZP-Site: true` header
3. If present, this domain is a PZP site
4. Fetch manifest from `X-PZP-Manifest` path
5. Proceed with PZP consumption

---

## 2. DNS TXT Discovery

### Purpose

DNS TXT records enable discovery from the main domain without fetching the PZP subdomain.

### Format

```
_pzp.example.com.  IN  TXT  "v=pzp1 endpoint=pzp.example.com type=documentation"
```

### Record Name

The TXT record MUST be at `_pzp.{domain}`:

| Domain | Record Name |
|--------|-------------|
| `example.com` | `_pzp.example.com` |
| `shop.example.com` | `_pzp.shop.example.com` |

### Record Fields

| Field | Required | Description | Example |
|-------|----------|-------------|---------|
| `v` | MUST | Protocol version | `pzp1` |
| `endpoint` | MUST | PZP hostname | `pzp.example.com` |
| `type` | SHOULD | Site type | `documentation` |
| `path` | MAY | Base path if not root | `/pzp` |

### Field Separator

Fields are separated by spaces. Values containing spaces MUST be quoted.

### Examples

**Standard subdomain:**
```
_pzp.example.com.  TXT  "v=pzp1 endpoint=pzp.example.com type=commerce"
```

**Same-domain path:**
```
_pzp.example.com.  TXT  "v=pzp1 endpoint=example.com path=/pzp type=api"
```

**Multiple PZP sites:**
```
_pzp.example.com.       TXT  "v=pzp1 endpoint=pzp.example.com type=documentation"
_pzp.shop.example.com.  TXT  "v=pzp1 endpoint=pzp.shop.example.com type=commerce"
```

### Agent Behavior

When an agent wants to check if a domain has PZP:

```
1. Query DNS for TXT record at _pzp.{domain}
2. Parse record fields
3. Verify v=pzp1
4. Construct PZP URL from endpoint (and path if present)
5. Fetch manifest from that URL
```

### DNS Lookup Example

```bash
$ dig _pzp.example.com TXT +short
"v=pzp1 endpoint=pzp.example.com type=documentation"
```

---

## 3. HTML Link Discovery

### Purpose

When agents are already parsing a human-facing page, they can discover the PZP alternative via HTML link elements.

### Format

```html
<link rel="pzp-site" href="https://pzp.example.com/">
```

### Attributes

| Attribute | Required | Description |
|-----------|----------|-------------|
| `rel` | MUST | Must be `pzp-site` |
| `href` | MUST | Full URL to PZP site root |
| `type` | MAY | Site type |
| `title` | MAY | Human-readable title |

### Placement

The link element SHOULD be in the `<head>` section:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Example Company</title>
    <link rel="pzp-site" href="https://pzp.example.com/">
    <!-- Other head elements -->
</head>
```

### Multiple PZP Sites

If a human site has multiple PZP counterparts:

```html
<link rel="pzp-site" href="https://pzp.example.com/" title="Main">
<link rel="pzp-site" href="https://pzp-api.example.com/" title="API" type="api">
```

### Agent Behavior

When an agent is parsing an HTML page:

1. Look for `<link rel="pzp-site">` in `<head>`
2. If found, extract `href` value
3. Optionally note `type` and `title`
4. Follow `href` to PZP site

---

## Discovery Priority

Agents SHOULD attempt discovery in this order:

```
┌─────────────────────────────────────────┐
│ 1. DNS TXT Record                       │
│    Query _pzp.{domain}                  │
│    └─ Found? → Use endpoint             │
├─────────────────────────────────────────┤
│ 2. robots.txt on target domain          │
│    GET {domain}/robots.txt              │
│    └─ Has X-PZP-Site? → PZP found       │
├─────────────────────────────────────────┤
│ 3. robots.txt on pzp subdomain          │
│    GET pzp.{domain}/robots.txt          │
│    └─ Has X-PZP-Site? → PZP found       │
├─────────────────────────────────────────┤
│ 4. HTML link element (if parsing HTML)  │
│    Find <link rel="pzp-site">           │
│    └─ Found? → Follow href              │
├─────────────────────────────────────────┤
│ 5. No PZP site available                │
│    Fall back to HTML parsing            │
└─────────────────────────────────────────┘
```

### Rationale

1. **DNS first:** Single lightweight query, no HTTP request
2. **Target robots.txt:** Site might have PZP at same domain
3. **Subdomain robots.txt:** Check conventional PZP subdomain
4. **HTML link:** Last resort when already parsing human page

---

## Verification

After discovery, agents SHOULD verify the PZP site is valid:

```
1. Fetch /manifest.json
   └─ MUST exist and be valid JSON

2. Check manifest.$schema
   └─ SHOULD reference pzp.riscent.com/schemas/manifest.schema.json

3. Verify HTTP headers
   └─ Response MUST include X-PZP-Site: true
```

### Verification Response

A valid PZP site returns:

```http
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
X-PZP-Site: true
X-PZP-Version: 1.0.0

{
  "$schema": "https://pzp.riscent.com/schemas/manifest.schema.json",
  "version": "1.0.0",
  "name": "Example Site",
  ...
}
```

---

## Error Handling

### Discovery Failures

| Condition | Agent Behavior |
|-----------|----------------|
| DNS query fails | Try next mechanism |
| robots.txt 404 | Try next mechanism |
| No X-PZP-Site header | Try next mechanism |
| manifest.json 404 | Site is not valid PZP |
| manifest.json invalid | Site is not valid PZP |

### Caching

Agents SHOULD cache discovery results:

| Data | TTL |
|------|-----|
| DNS TXT record | Respect DNS TTL |
| robots.txt | 1 hour default |
| Manifest | Respect Cache-Control |

---

## Implementation Examples

### Setting Up DNS TXT

**Cloudflare:**
1. Go to DNS settings
2. Add record:
   - Type: TXT
   - Name: `_pzp`
   - Content: `v=pzp1 endpoint=pzp.yourdomain.com type=documentation`

**AWS Route 53:**
```json
{
  "Type": "TXT",
  "Name": "_pzp.example.com",
  "TTL": 300,
  "ResourceRecords": [
    { "Value": "\"v=pzp1 endpoint=pzp.example.com type=documentation\"" }
  ]
}
```

### Setting Up robots.txt

**Static file:**
```
User-agent: *
Allow: /

X-PZP-Site: true
X-PZP-Manifest: /manifest.json
X-PZP-Version: 1.0.0
```

**Vercel (vercel.json headers):**
```json
{
  "headers": [
    {
      "source": "/robots.txt",
      "headers": [
        { "key": "X-PZP-Site", "value": "true" }
      ]
    }
  ]
}
```

### Adding HTML Link

**In HTML head:**
```html
<link rel="pzp-site" href="https://pzp.example.com/">
```

**In Next.js:**
```jsx
import Head from 'next/head'

export default function Layout({ children }) {
  return (
    <>
      <Head>
        <link rel="pzp-site" href="https://pzp.example.com/" />
      </Head>
      {children}
    </>
  )
}
```

---

## Security Considerations

### Validation

Agents MUST validate discovered PZP sites:
- Verify HTTPS (HTTP SHOULD be rejected)
- Verify manifest structure
- Verify response headers

### Spoofing Prevention

To prevent malicious PZP sites:
- DNS TXT records are controlled by domain owner
- robots.txt is served by the domain
- HTML links require page access

### Privacy

Discovery queries may reveal agent behavior:
- DNS queries are typically cached
- robots.txt fetches are common
- Consider privacy implications of HTML parsing

---

*Discovery is the first step. Make it effortless.*
