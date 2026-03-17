# Platform Implementation Guides

> Add PZP to popular CMS and e-commerce platforms.

## Overview

These guides cover PZP implementation for popular no-code and low-code platforms. Each guide provides platform-specific instructions, plugin recommendations, and workarounds for platform limitations.

---

## Platform Categories

### Content Management Systems

| Platform | Market Share | Complexity | Guide |
|----------|--------------|------------|-------|
| WordPress | ~43% of web | Low-Medium | [WordPress](wordpress.md) |
| Ghost | Growing | Low | [Ghost](ghost.md) |
| Wix | ~3% | Medium | [Wix](wix.md) |
| Squarespace | ~2% | Medium | [Squarespace](squarespace.md) |
| Webflow | ~1% | Low-Medium | [Webflow](webflow.md) |

### E-commerce Platforms

| Platform | Market Share | Complexity | Guide |
|----------|--------------|------------|-------|
| Shopify | ~28% e-commerce | Low-Medium | [Shopify](shopify.md) |
| WooCommerce | ~36% e-commerce | Low | [WordPress](wordpress.md) |
| BigCommerce | ~6% | Medium | Coming soon |

### Headless CMS

| Platform | Type | Complexity | Guide |
|----------|------|------------|-------|
| Contentful | Headless | Low | [Contentful](contentful.md) |
| Strapi | Self-hosted | Low | [Strapi](strapi.md) |
| Sanity | Headless | Low | [Sanity](sanity.md) |

---

## Implementation Approaches

### Approach 1: Subdomain Hosting

Host PZP separately from your main platform:

```
www.yoursite.com          → WordPress/Shopify (human site)
pzp.yoursite.com          → Vercel/Netlify (PZP site)
```

**Best for:** Any platform, maximum control, dynamic content

**Steps:**
1. Generate PZP content from platform data via API
2. Host on separate static/dynamic host
3. Configure DNS subdomain
4. Sync content on publish/schedule

### Approach 2: Path-Based

Serve PZP from a path on your existing site:

```
www.yoursite.com          → Human site
www.yoursite.com/pzp/     → PZP content
```

**Best for:** WordPress, platforms with file access

**Steps:**
1. Add PZP files to appropriate directory
2. Configure routing/redirects
3. Set headers if possible

### Approach 3: Plugin/App

Use platform-specific plugins:

**Best for:** WordPress, Shopify

**Options:**
- WordPress: Custom plugin or static file serving
- Shopify: App or theme modifications

---

## Quick Reference

### WordPress

```
wp-content/pzp/           # PZP files location
or
pzp.yoursite.com/         # Subdomain (recommended)
```

Use WP REST API to generate PZP data files.

### Shopify

```
pzp.yourstore.com/        # Subdomain (recommended)
```

Use Shopify Admin API to sync products to PZP.

### Ghost

```
pzp.yourblog.com/         # Subdomain (recommended)
```

Use Ghost Content API to generate article index.

### Headless CMS

All headless CMS can generate PZP directly:
- Build PZP files during static site generation
- Trigger rebuild on content publish
- Include PZP in deployment

---

## Platform Limitations

| Platform | Custom Headers | JSON Files | Subdomain |
|----------|---------------|------------|-----------|
| WordPress | Yes (plugin) | Yes | Yes |
| Shopify | Limited | Via theme | Yes |
| Wix | No | No | Yes |
| Squarespace | No | Limited | Yes |
| Webflow | Yes | Yes | Yes |
| Ghost | Yes (config) | Yes | Yes |

### Workarounds

**No custom headers:**
Use subdomain approach where you have full control.

**No JSON files:**
Host PZP on separate infrastructure.

**Limited static files:**
Use CDN or separate hosting for PZP.

---

## Recommended Approach by Platform

| Platform | Recommended Approach |
|----------|---------------------|
| WordPress (self-hosted) | Path-based or subdomain |
| WordPress.com | Subdomain only |
| Shopify | Subdomain with API sync |
| Wix | Subdomain only |
| Squarespace | Subdomain only |
| Webflow | Path-based or subdomain |
| Ghost (Pro) | Subdomain |
| Ghost (self-hosted) | Path-based |
| Contentful | Build-time generation |
| Strapi | API-based generation |
| Sanity | Build-time or API |

---

## Getting Started

1. Choose your platform guide from the list above
2. Decide on subdomain vs path-based approach
3. Follow platform-specific setup instructions
4. Test with PZP validator
5. Monitor agent access

---

*PZP works with any platform. Choose the approach that fits your setup.*
