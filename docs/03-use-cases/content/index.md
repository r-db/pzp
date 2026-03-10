# Content Site PZP Implementation

> How to implement PZP for blogs, news sites, media platforms, and editorial content.

## Overview

Content sites—blogs, news publications, podcasts, video channels—are prime candidates for PZP. AI agents increasingly answer questions by synthesizing content from multiple sources. Without PZP, your content gets parsed poorly or ignored entirely.

With PZP, your content becomes:
- Efficiently consumable by AI systems
- Properly attributed in AI responses
- Discoverable by agent crawlers
- Structured for accurate extraction

---

## Why Content Sites Need PZP

### The Zero-Click Crisis

Content sites are the hardest hit by AI search:
- **69%** of searches end without a click
- **61%** CTR drop with AI Overviews
- **33%** traffic loss for publishers in one year

### The Attribution Problem

When AI synthesizes content:
- Your content informs the answer
- You may not receive attribution
- You definitely don't receive traffic
- Your monetization model breaks

### The PZP Advantage

With PZP:
- Clean, structured content for accurate extraction
- Clear metadata for proper attribution
- Direct agent access (no scraping)
- Fresh content signals for recency

---

## Content Site PZP Structure

```
pzp.blog.example.com/
├── index.md                    # Blog entry point
├── manifest.json               # Site manifest
├── robots.txt                  # Discovery
│
├── context/
│   ├── essential.md            # Blog overview (~400 tokens)
│   └── full.md                 # Complete context
│
├── actions/
│   └── index.json              # Search, list, subscribe
│
├── data/
│   ├── index.json              # Content catalog
│   ├── articles/
│   │   ├── recent.json         # Recent articles
│   │   ├── popular.json        # Most popular
│   │   └── {slug}.md           # Individual articles
│   ├── categories.json         # Category tree
│   └── authors.json            # Author information
│
└── meta/
    ├── version.json
    └── changelog.md
```

---

## Documents in This Section

| Document | Description |
|----------|-------------|
| [Blog](blog.md) | Blog implementation guide |
| [News](news.md) | News publication patterns |
| [Media](media.md) | Video and podcast metadata |
| [Wiki](wiki.md) | Encyclopedia and wiki structure |
| [Complete Example](example-complete.md) | Full blog PZP site |

---

## Key Actions for Content Sites

### List Articles

```json
{
  "name": "list-articles",
  "description": "List articles with filters",
  "endpoint": "/api/articles",
  "method": "GET",
  "input": {
    "category": { "type": "string", "description": "Filter by category" },
    "author": { "type": "string", "description": "Filter by author" },
    "since": { "type": "string", "description": "ISO date, articles after" },
    "limit": { "type": "integer", "default": 20 },
    "offset": { "type": "integer", "default": 0 }
  }
}
```

### Search Content

```json
{
  "name": "search-content",
  "description": "Full-text search across all content",
  "endpoint": "/api/search",
  "method": "GET",
  "input": {
    "query": { "type": "string", "required": true },
    "type": { "type": "string", "enum": ["article", "page", "all"] },
    "sort": { "type": "string", "enum": ["relevance", "date", "popularity"] }
  }
}
```

### Get Article

```json
{
  "name": "get-article",
  "description": "Get full article content",
  "endpoint": "/data/articles/{slug}.md",
  "method": "GET",
  "input": {
    "slug": { "type": "string", "required": true }
  }
}
```

---

## Article Schema

### Article Metadata

```json
{
  "id": "art_2026_03_06_pzp_launch",
  "slug": "announcing-pzp-1-0",
  "title": "Announcing Prime Zero Protocol 1.0",
  "description": "The machine-native web is here.",
  "author": {
    "id": "auth_ryan",
    "name": "Ryan",
    "url": "/authors/ryan"
  },
  "category": "announcements",
  "tags": ["pzp", "launch", "protocol"],

  "dates": {
    "published": "2026-03-06T10:00:00Z",
    "updated": "2026-03-06T14:30:00Z"
  },

  "metrics": {
    "readTime": "5 min",
    "wordCount": 1200,
    "tokens": 1600
  },

  "content": "/data/articles/announcing-pzp-1-0.md"
}
```

### Article Content (Markdown)

```markdown
---
id: art_2026_03_06_pzp_launch
title: Announcing Prime Zero Protocol 1.0
author: Ryan
published: 2026-03-06T10:00:00Z
category: announcements
tags: [pzp, launch, protocol]
---

# Announcing Prime Zero Protocol 1.0

The machine-native web is here.

## Introduction

Today we're launching Prime Zero Protocol (PZP), a new standard for...

## What is PZP?

PZP defines how websites should structure content for AI consumption...

## Why This Matters

[Article content continues...]
```

---

## Token Efficiency for Articles

### Summary vs. Full

| Content Level | Token Budget | Use Case |
|---------------|--------------|----------|
| Headline + meta | 50-100 | Listing, search results |
| Summary | 200-300 | Quick understanding |
| Full article | 1000-3000 | Complete consumption |

### Hierarchical Access

```json
{
  "articles": [
    {
      "id": "art_123",
      "title": "Article Title",
      "summary": "Brief 2-3 sentence summary for quick consumption",
      "fullContent": "/data/articles/article-slug.md",
      "tokens": {
        "summary": 50,
        "full": 1500
      }
    }
  ]
}
```

---

## Freshness Signals

### Why Freshness Matters

AI systems prioritize recent content. Signal freshness through:

### HTTP Headers

```http
Last-Modified: Wed, 06 Mar 2026 14:30:00 GMT
ETag: "abc123"
Cache-Control: public, max-age=3600
```

### Manifest Update

```json
{
  "updated": "2026-03-06T14:30:00Z"
}
```

### Feed Endpoints

```json
{
  "name": "recent-articles",
  "description": "Most recently published articles",
  "endpoint": "/data/articles/recent.json",
  "cacheControl": "max-age=300"
}
```

---

## Attribution Support

### Article Attribution

Include clear attribution data:

```json
{
  "attribution": {
    "source": "Tech Blog",
    "author": "Ryan",
    "url": "https://blog.example.com/article-slug",
    "license": "CC-BY-4.0",
    "citation": "Ryan. (2026). Article Title. Tech Blog."
  }
}
```

### Preferred Citation

```json
{
  "preferredCitation": "Please cite as: 'Article Title' by Ryan, Tech Blog (2026)"
}
```

---

## Content Types

### Blog Posts

Personal or company blogs with dated entries.

**Characteristics:**
- Date-ordered content
- Author attribution
- Categories and tags
- Comments/discussion

### News Articles

Time-sensitive journalistic content.

**Characteristics:**
- Publication timestamps critical
- Bylines and sources
- Updates and corrections
- Related articles

### Tutorials/Guides

Educational step-by-step content.

**Characteristics:**
- Structured steps
- Code examples
- Prerequisites
- Versioning

### Podcasts/Video

Audio and video content with metadata.

**Characteristics:**
- Episode metadata
- Transcripts
- Show notes
- Duration

---

## Common Patterns

### Category Navigation

```json
{
  "categories": [
    {
      "id": "technology",
      "name": "Technology",
      "articleCount": 150,
      "subcategories": [
        { "id": "ai", "name": "AI & Machine Learning", "articleCount": 45 },
        { "id": "web", "name": "Web Development", "articleCount": 60 }
      ]
    }
  ]
}
```

### Author Profiles

```json
{
  "authors": [
    {
      "id": "auth_ryan",
      "name": "Ryan",
      "bio": "Founder and principal engineer",
      "articleCount": 75,
      "topics": ["AI", "protocols", "architecture"],
      "social": {
        "twitter": "@ryan"
      }
    }
  ]
}
```

### Related Content

```json
{
  "article": {
    "id": "art_123",
    "related": [
      { "id": "art_120", "title": "Previous Post", "similarity": 0.85 },
      { "id": "art_125", "title": "Follow-up Post", "similarity": 0.78 }
    ]
  }
}
```

---

## Integration with Human Site

### Parallel Content

```
blog.example.com/article-slug     # Human-readable HTML
pzp.blog.example.com/data/articles/article-slug.md  # Machine-readable
```

### Synced Updates

When human site updates:
1. Update PZP content
2. Update `manifest.json` timestamp
3. Update `recent.json` feed

### Content Pipeline

```
CMS → Generate HTML (human site)
    → Generate MD (PZP site)
    → Update indexes
    → Deploy both
```

---

## Success Metrics

| Metric | Description |
|--------|-------------|
| Agent citations | Times your content is cited by AI |
| PZP fetches | Direct access via PZP endpoints |
| Token efficiency | Avg tokens per article delivery |
| Freshness delta | Time from publish to PZP availability |
| Attribution rate | Proper citations in AI responses |

---

*Your content deserves proper extraction. Give agents what they need.*
