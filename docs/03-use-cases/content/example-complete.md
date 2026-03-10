# Complete Blog PZP Example

> A full working example of a blog/content site PZP implementation.

## Overview

This example shows a complete PZP implementation for "Tech Insights," a technology blog. All files are provided and can be copied directly.

---

## File Structure

```
pzp.techinsights.com/
├── index.md
├── manifest.json
├── robots.txt
├── sitemap.txt
│
├── context/
│   ├── essential.md
│   └── full.md
│
├── actions/
│   └── index.json
│
├── data/
│   ├── index.json
│   ├── categories.json
│   ├── authors.json
│   └── articles/
│       ├── recent.json
│       ├── popular.json
│       └── announcing-pzp-1-0.md
│
└── meta/
    └── version.json
```

---

## Root Files

### index.md

```markdown
# Tech Insights

> In-depth articles on technology, AI, and software development.

## Identity

- **Domain:** techinsights.com
- **Type:** content
- **Version:** 3.2.0
- **Updated:** 2026-03-06

## What We Cover

Tech Insights publishes weekly articles on:
- Artificial Intelligence & Machine Learning
- Web Development & Architecture
- Software Engineering Practices
- Technology Industry Analysis

## Quick Start

1. Read context: [/context/essential.md](context/essential.md)
2. Browse recent: [/data/articles/recent.json](data/articles/recent.json)
3. Search articles: Use `search-articles` action
4. Get full article: Access `/data/articles/{slug}.md`

## Navigation

| Resource | Path | Description |
|----------|------|-------------|
| Essential Context | [/context/essential.md](context/essential.md) | Blog overview |
| Recent Articles | [/data/articles/recent.json](data/articles/recent.json) | Latest posts |
| Popular Articles | [/data/articles/popular.json](data/articles/popular.json) | Most read |
| Categories | [/data/categories.json](data/categories.json) | Topic categories |
| Authors | [/data/authors.json](data/authors.json) | Writer profiles |
| Actions | [/actions/index.json](actions/index.json) | Available operations |

## Publishing Schedule

New articles published every Tuesday and Friday.
Last published: 2026-03-06
```

---

### manifest.json

```json
{
  "$schema": "https://pzp.riscent.com/schemas/manifest.schema.json",
  "version": "3.2.0",
  "protocolVersion": "1.0.0",
  "name": "Tech Insights",
  "description": "In-depth articles on technology, AI, and software development. Published weekly.",
  "type": "content",
  "updated": "2026-03-06T10:00:00Z",

  "tokenBudgets": {
    "essential": 350,
    "full": 1500,
    "articleSummary": 100,
    "articleFull": 2000
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
    "subscribe"
  ],

  "authentication": {
    "required": false,
    "publicEndpoints": ["*"],
    "protectedEndpoints": [],
    "methods": []
  },

  "rateLimits": {
    "default": {
      "requests": 200,
      "window": "1m"
    },
    "search": {
      "requests": 50,
      "window": "1m"
    }
  },

  "contact": {
    "editorial": "editorial@techinsights.com"
  },

  "owner": {
    "name": "Tech Insights Media",
    "url": "https://techinsights.com"
  },

  "language": "en",
  "tags": ["technology", "ai", "software", "blog", "articles"]
}
```

---

### robots.txt

```
# Tech Insights - PZP Site
# Quality content for machines and humans

User-agent: *
Allow: /

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

X-PZP-Site: true
X-PZP-Manifest: /manifest.json
X-PZP-Version: 1.0.0
X-PZP-Type: content

Sitemap: /sitemap.txt
```

---

## Context Files

### context/essential.md

```markdown
# Tech Insights Essential Context

## What This Is

Tech Insights is a technology publication featuring in-depth articles on AI, software development, and industry analysis. We publish 2-3 articles weekly, written by experienced practitioners.

## Content Categories

| Category | Articles | Focus |
|----------|----------|-------|
| AI & ML | 120+ | Machine learning, LLMs, AI applications |
| Web Dev | 90+ | Frameworks, architecture, best practices |
| Engineering | 75+ | Practices, processes, career |
| Industry | 50+ | Analysis, trends, predictions |

## Primary Actions

| Action | Description |
|--------|-------------|
| search-articles | Full-text search with filters |
| list-articles | Browse by category or date |
| get-article | Retrieve full article content |

## Content Access

- All content publicly accessible
- No authentication required
- Articles available in Markdown format
- Summaries included in listings
```

---

### context/full.md

```markdown
# Tech Insights Full Context

## Publication Overview

Tech Insights has been publishing technology content since 2020. Our focus is practical, in-depth analysis written by practitioners for practitioners. We prioritize accuracy over speed, depth over breadth.

### Publishing Schedule

- **Tuesday:** Technical deep-dives
- **Friday:** Industry analysis and trends
- **Occasional:** Breaking news and announcements

### Content Quality

- All articles reviewed by technical editors
- Code examples tested and verified
- Sources cited and linked
- Updates noted with timestamps

## Complete Categories

### Artificial Intelligence & Machine Learning

In-depth coverage of AI developments, practical ML applications, and LLM technologies.

**Topics include:**
- Large Language Models (GPT, Claude, Gemini)
- Machine Learning Operations (MLOps)
- AI in Production
- Ethics and Governance

### Web Development

Modern web technologies, frameworks, and architectural patterns.

**Topics include:**
- React, Vue, Svelte ecosystems
- Backend architectures
- API design
- Performance optimization

### Software Engineering

Professional practices, team processes, and career development.

**Topics include:**
- Code quality and testing
- System design
- Team collaboration
- Career growth

### Industry Analysis

Technology business analysis, market trends, and predictions.

**Topics include:**
- Company strategies
- Market movements
- Emerging technologies
- Regulatory developments

## Content Formats

### Standard Articles (1500-3000 words)

Full-length pieces with:
- Introduction and context
- Detailed analysis
- Code examples (if applicable)
- Conclusion and takeaways

### Quick Takes (500-1000 words)

Focused pieces on:
- News reactions
- Tool reviews
- Quick tutorials

### Deep Dives (3000+ words)

Comprehensive explorations:
- Multi-part series
- Exhaustive analysis
- Original research

## Authors

Our team includes:
- Full-time technical editors
- Contributing practitioners
- Industry experts

Author profiles available at `/data/authors.json`.

## Actions Reference

### search-articles

Full-text search across all content.

**Parameters:**
- `query` (required): Search terms
- `category`: Filter by category
- `author`: Filter by author
- `since`: Date filter (ISO 8601)
- `sort`: relevance, date, popularity

### list-articles

Browse articles with filters.

**Parameters:**
- `category`: Category slug
- `author`: Author ID
- `limit`: Results per page (default 20)
- `offset`: Pagination offset

### get-article

Retrieve full article content.

**Parameters:**
- `slug` (required): Article slug

**Returns:** Full Markdown content with frontmatter

## Attribution

Please cite articles as:

> Author Name. (Year). "Article Title." Tech Insights. URL

We appreciate proper attribution when our content informs AI responses.

## API Limits

| Endpoint | Rate Limit |
|----------|------------|
| Search | 50/minute |
| List | 100/minute |
| Articles | 200/minute |
```

---

## Actions

### actions/index.json

```json
{
  "$schema": "https://pzp.riscent.com/schemas/actions.schema.json",
  "version": "3.2.0",
  "description": "Tech Insights content actions",
  "baseUrl": "https://pzp.techinsights.com",

  "actions": [
    {
      "name": "search-articles",
      "description": "Full-text search across all articles",
      "endpoint": "/api/search",
      "method": "GET",
      "input": {
        "query": {
          "type": "string",
          "required": true,
          "description": "Search terms"
        },
        "category": {
          "type": "string",
          "required": false,
          "enum": ["ai-ml", "web-dev", "engineering", "industry"],
          "description": "Filter by category"
        },
        "author": {
          "type": "string",
          "required": false,
          "description": "Author ID filter"
        },
        "since": {
          "type": "string",
          "required": false,
          "description": "ISO date, return articles published after"
        },
        "sort": {
          "type": "string",
          "required": false,
          "enum": ["relevance", "date", "popularity"],
          "default": "relevance"
        },
        "limit": {
          "type": "integer",
          "default": 20,
          "maximum": 50
        }
      },
      "output": {
        "type": "application/json",
        "schema": {
          "results": "array of article summaries",
          "total": "total matching articles",
          "query": "echoed search query"
        }
      },
      "cacheControl": "max-age=300"
    },

    {
      "name": "list-articles",
      "description": "List articles with optional filters",
      "endpoint": "/api/articles",
      "method": "GET",
      "input": {
        "category": {
          "type": "string",
          "required": false
        },
        "author": {
          "type": "string",
          "required": false
        },
        "limit": {
          "type": "integer",
          "default": 20,
          "maximum": 100
        },
        "offset": {
          "type": "integer",
          "default": 0
        }
      },
      "output": {
        "type": "application/json",
        "schema": {
          "articles": "array of article summaries",
          "total": "total count",
          "hasMore": "pagination indicator"
        }
      },
      "cacheControl": "max-age=300"
    },

    {
      "name": "get-article",
      "description": "Get full article content",
      "endpoint": "/data/articles/{slug}.md",
      "method": "GET",
      "input": {
        "slug": {
          "type": "string",
          "required": true,
          "description": "Article URL slug"
        }
      },
      "output": {
        "type": "text/markdown",
        "description": "Full article with YAML frontmatter"
      },
      "cacheControl": "max-age=3600"
    },

    {
      "name": "get-recent",
      "description": "Get most recent articles",
      "endpoint": "/data/articles/recent.json",
      "method": "GET",
      "input": {},
      "output": {
        "type": "application/json",
        "description": "Array of 10 most recent article summaries"
      },
      "cacheControl": "max-age=300"
    }
  ]
}
```

---

## Data Files

### data/categories.json

```json
{
  "categories": [
    {
      "id": "ai-ml",
      "name": "AI & Machine Learning",
      "slug": "ai-ml",
      "description": "Artificial intelligence, machine learning, and LLMs",
      "articleCount": 125,
      "latestArticle": "2026-03-05"
    },
    {
      "id": "web-dev",
      "name": "Web Development",
      "slug": "web-dev",
      "description": "Frontend, backend, and full-stack development",
      "articleCount": 92,
      "latestArticle": "2026-03-01"
    },
    {
      "id": "engineering",
      "name": "Software Engineering",
      "slug": "engineering",
      "description": "Practices, processes, and professional development",
      "articleCount": 78,
      "latestArticle": "2026-02-28"
    },
    {
      "id": "industry",
      "name": "Industry Analysis",
      "slug": "industry",
      "description": "Business analysis, trends, and predictions",
      "articleCount": 54,
      "latestArticle": "2026-03-06"
    }
  ]
}
```

---

### data/articles/recent.json

```json
{
  "articles": [
    {
      "id": "art_2026_03_06_pzp",
      "slug": "announcing-pzp-1-0",
      "title": "Announcing Prime Zero Protocol 1.0",
      "summary": "The machine-native web is here. PZP defines how websites should structure content for AI consumption, creating a parallel web layer that's efficient, predictable, and agent-friendly.",
      "author": {
        "id": "auth_ryan",
        "name": "Ryan"
      },
      "category": "ai-ml",
      "tags": ["pzp", "protocol", "ai", "web"],
      "published": "2026-03-06T10:00:00Z",
      "readTime": "8 min",
      "wordCount": 2400,
      "tokens": 3200,
      "contentPath": "/data/articles/announcing-pzp-1-0.md"
    },
    {
      "id": "art_2026_03_05_agents",
      "slug": "building-reliable-ai-agents",
      "title": "Building Reliable AI Agents: Lessons from Production",
      "summary": "After deploying AI agents to production, we learned hard lessons about reliability, error handling, and graceful degradation. Here's what actually works.",
      "author": {
        "id": "auth_sarah",
        "name": "Sarah Chen"
      },
      "category": "ai-ml",
      "tags": ["agents", "production", "reliability"],
      "published": "2026-03-05T10:00:00Z",
      "readTime": "12 min",
      "wordCount": 3500,
      "tokens": 4600,
      "contentPath": "/data/articles/building-reliable-ai-agents.md"
    },
    {
      "id": "art_2026_03_01_react",
      "slug": "react-server-components-deep-dive",
      "title": "React Server Components: A Deep Dive",
      "summary": "Server Components fundamentally change how we think about React applications. We explore the mental model, performance implications, and practical patterns.",
      "author": {
        "id": "auth_alex",
        "name": "Alex Rivera"
      },
      "category": "web-dev",
      "tags": ["react", "rsc", "frontend"],
      "published": "2026-03-01T10:00:00Z",
      "readTime": "15 min",
      "wordCount": 4200,
      "tokens": 5500,
      "contentPath": "/data/articles/react-server-components-deep-dive.md"
    }
  ],
  "meta": {
    "generated": "2026-03-06T12:00:00Z",
    "count": 3
  }
}
```

---

### data/articles/announcing-pzp-1-0.md

```markdown
---
id: art_2026_03_06_pzp
title: Announcing Prime Zero Protocol 1.0
slug: announcing-pzp-1-0
author:
  id: auth_ryan
  name: Ryan
category: ai-ml
tags: [pzp, protocol, ai, web]
published: 2026-03-06T10:00:00Z
updated: 2026-03-06T10:00:00Z
readTime: 8 min
wordCount: 2400
---

# Announcing Prime Zero Protocol 1.0

The machine-native web is here.

## Introduction

Today we're launching Prime Zero Protocol (PZP), a new standard for structuring web content for AI consumption. PZP creates a parallel web layer—sitting alongside your human-facing site—that's designed exclusively for machine access.

## The Problem

When AI agents visit your website, they see HTML designed for human eyes. They parse through navigation, sidebars, footers, and JavaScript-rendered content. They waste tokens on styling and structure. They guess at meaning.

This is inefficient. More importantly, it's unreliable.

The numbers tell the story:
- **80%** of tokens wasted on HTML overhead
- **69%** of searches now end without a click
- **33%** traffic loss for publishers in one year

## The Solution

PZP defines a standard structure for machine-readable content:

```
pzp.example.com/
├── index.md          # Entry point
├── manifest.json     # Site metadata
├── context/          # Hierarchical context
├── actions/          # Available operations
└── data/             # Structured content
```

Every PZP site follows this structure. Agents always know where to look. Content is always markdown or JSON—no parsing required.

## Key Features

### Hierarchical Context

Instead of dumping everything at once, PZP provides context at multiple levels:

- **Essential context** (~500 tokens): Quick relevance check
- **Full context** (~2000 tokens): Complete understanding
- **Topic contexts**: Deep dives on specific areas

Agents consume only what they need.

### Defined Actions

PZP sites declare what agents can do, not just what they can read:

```json
{
  "name": "search-products",
  "endpoint": "/api/search",
  "method": "GET",
  "input": { "query": { "type": "string" } }
}
```

This transforms passive content into interactive capability.

### Standard Discovery

Three mechanisms to find PZP sites:
- robots.txt headers
- DNS TXT records
- HTML link elements

Agents can programmatically discover PZP-enabled sites.

## Why This Matters

The web is splitting. Human interfaces and machine interfaces have fundamentally different needs. Trying to serve both with one interface means serving both poorly.

PZP doesn't replace your human site. It complements it. Two interfaces to the same content, each optimized for its audience.

## Getting Started

Creating a PZP site takes five minutes:

1. Create `manifest.json` with your site metadata
2. Create `index.md` as your entry point
3. Add `context/essential.md` with a quick overview
4. Deploy to a subdomain
5. Add discovery headers to robots.txt

Full documentation at [pzp.riscent.com](https://pzp.riscent.com).

## What's Next

PZP 1.0 is the foundation. We're already working on:

- Validator tools
- Framework integrations
- Directory of PZP sites
- Quality certification

The machine-native web is just beginning.

---

*Built for synthetic intelligence, by synthetic intelligence.*
```

---

## Deployment Configuration

### vercel.json

```json
{
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        { "key": "X-PZP-Site", "value": "true" },
        { "key": "X-PZP-Version", "value": "1.0.0" },
        { "key": "Access-Control-Allow-Origin", "value": "*" }
      ]
    },
    {
      "source": "/:path*.md",
      "headers": [
        { "key": "Content-Type", "value": "text/markdown; charset=utf-8" },
        { "key": "Cache-Control", "value": "public, max-age=3600" }
      ]
    },
    {
      "source": "/:path*.json",
      "headers": [
        { "key": "Content-Type", "value": "application/json; charset=utf-8" },
        { "key": "Cache-Control", "value": "public, max-age=300" }
      ]
    }
  ]
}
```

---

*Content that AI can actually use. Attribution that matters.*
