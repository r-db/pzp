# Terminology

> Definitions of terms used throughout the Prime Zero Protocol specification.

## Requirements Keywords

This specification uses keywords as defined in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119.html):

### MUST

This word, or the terms "REQUIRED" or "SHALL", mean that the definition is an absolute requirement of the specification.

**Example:** "The manifest MUST include a `version` field."

### MUST NOT

This phrase, or the phrase "SHALL NOT", mean that the definition is an absolute prohibition of the specification.

**Example:** "The essential context MUST NOT exceed 1000 tokens."

### SHOULD

This word, or the adjective "RECOMMENDED", mean that there may exist valid reasons in particular circumstances to ignore a particular item, but the full implications must be understood and carefully weighed before choosing a different course.

**Example:** "The site SHOULD provide hierarchical context."

### SHOULD NOT

This phrase, or the phrase "NOT RECOMMENDED", mean that there may exist valid reasons in particular circumstances when the particular behavior is acceptable or even useful, but the full implications should be understood and the case carefully weighed before implementing any behavior described with this label.

**Example:** "Sites SHOULD NOT require authentication for discovery endpoints."

### MAY

This word, or the adjective "OPTIONAL", mean that an item is truly optional. One implementer may choose to include the item because a particular marketplace requires it or because the implementer feels that it enhances the product while another implementer may omit the same item.

**Example:** "The site MAY include topic-specific context files."

---

## PZP-Specific Terms

### Agent

A software system that autonomously consumes web content on behalf of users. Agents include:
- Large language models (LLMs) accessing web content
- AI assistants retrieving information
- Autonomous agents executing tasks
- Crawlers indexing machine-readable content

### Consuming Agent

An agent that is actively retrieving and processing content from a PZP site.

### Context

Structured content that provides background information about a PZP site. Context is hierarchical:
- **Essential context:** Minimum viable context (~500 tokens)
- **Full context:** Complete context (~2000-3000 tokens)
- **Topic context:** Specific subject matter context

### Context Budget

See [Token Budget](#token-budget).

### Discovery

The process by which an agent identifies that a site implements PZP and locates the PZP endpoint. Discovery mechanisms include:
- robots.txt headers
- DNS TXT records
- HTML link elements

### Endpoint

A specific URL path on a PZP site that serves content or accepts actions.

### Entry Point

The `index.md` file at the root of a PZP site. This is the first file an agent SHOULD read when consuming the site.

### Essential Context

A concise summary of what the PZP site offers, constrained to approximately 500 tokens. This provides enough information for an agent to determine relevance and next steps without consuming the full context.

Located at: `/context/essential.md`

### Full Context

Complete contextual information about the PZP site, including all capabilities, constraints, and detailed documentation. Typically 2000-3000 tokens.

Located at: `/context/full.md`

### Human Site

The traditional, human-facing website that a PZP site typically parallels. The human site serves browsers and human users; the PZP site serves agents.

### Machine-Native

Content designed primarily for machine consumption. Machine-native content:
- Uses structured formats (Markdown, JSON)
- Avoids presentation markup (HTML, CSS)
- Prioritizes information density
- Follows predictable patterns

### Manifest

A JSON file that describes the PZP site's identity, capabilities, and configuration. Every PZP site MUST have a manifest.

Located at: `/manifest.json`

### PZP Site

A domain or subdomain that implements the Prime Zero Protocol. A PZP site:
- Follows the PZP file structure
- Includes required files (index.md, manifest.json, robots.txt)
- Responds with required headers
- Provides machine-native content

### Site Type

A classification that describes the primary purpose of a PZP site:

| Type | Description |
|------|-------------|
| `service` | Provides executable actions or services |
| `documentation` | Reference material and guides |
| `data` | Dataset access and data APIs |
| `api` | Full CRUD API documentation |
| `knowledge` | Knowledge base or encyclopedia |
| `commerce` | E-commerce catalog and transactions |
| `content` | Blog, news, or editorial content |

### Token

The fundamental unit of measurement for language model consumption. Tokens are:
- Approximately 0.75 words per token (or 1.3 tokens per word)
- The basis for API pricing
- The unit of context window measurement

### Token Budget

The maximum number of tokens that a piece of content SHOULD consume. Token budgets ensure efficient agent consumption:

| Content Type | Typical Budget |
|--------------|----------------|
| Essential context | 300-500 tokens |
| Full context | 1500-3000 tokens |
| Topic context | 200-500 tokens |
| Action description | 100-200 tokens |

### Token Efficiency

A measure of information density. High token efficiency means maximum useful information per token consumed. PZP sites achieve token efficiency by:
- Using markdown instead of HTML
- Eliminating navigation and presentation
- Structuring content predictably
- Providing hierarchical context

---

## Protocol Components

### Actions

Defined operations that agents can execute on a PZP site. Actions have:
- **Name:** Unique identifier
- **Endpoint:** URL path
- **Method:** HTTP method (GET, POST, etc.)
- **Input:** Parameter schema
- **Output:** Response schema

Located at: `/actions/index.json`

### Data

Structured data resources available on the PZP site. Data files define:
- Available datasets
- Schema definitions
- Access patterns
- Query capabilities

Located at: `/data/` directory

### Meta

Metadata about the PZP site itself, including:
- Version information
- Changelog
- Health status

Located at: `/meta/` directory

### Schemas

JSON Schema definitions that validate PZP files:

| Schema | Validates |
|--------|-----------|
| `manifest.schema.json` | manifest.json |
| `actions.schema.json` | actions/index.json |
| `data.schema.json` | data/* files |
| `version.schema.json` | meta/version.json |

Located at: `/schemas/` directory (or hosted at pzp.riscent.com/schemas/)

---

## Network & HTTP Terms

### Base URL

The root URL of a PZP site (e.g., `https://pzp.example.com/`).

### Content-Type

The HTTP header indicating the media type of the response:

| Content | Content-Type |
|---------|--------------|
| Markdown | `text/markdown; charset=utf-8` |
| JSON | `application/json; charset=utf-8` |
| Plain text | `text/plain; charset=utf-8` |

### Discovery Headers

HTTP response headers that indicate PZP compliance:

| Header | Value | Required |
|--------|-------|----------|
| `X-PZP-Site` | `true` | MUST |
| `X-PZP-Version` | Protocol version (e.g., `1.0.0`) | MUST |
| `X-PZP-Tokens` | Token count for response | SHOULD |

### Rate Limiting

Mechanism to control the frequency of agent requests:

| Header | Description |
|--------|-------------|
| `X-RateLimit-Limit` | Maximum requests per window |
| `X-RateLimit-Remaining` | Requests remaining |
| `X-RateLimit-Reset` | Window reset timestamp |

---

## Conformance Terms

### Compliant

A PZP site is compliant if it meets all MUST requirements of the specification at a given conformance level.

### Conformance Level

The degree to which a PZP site implements the specification:

| Level | Description |
|-------|-------------|
| Minimal | Required files and headers only |
| Standard | Minimal + context + actions |
| Extended | Standard + data + meta + topic contexts |

### Validation

The process of verifying that PZP files conform to their schemas and that the site meets conformance requirements.

### Validator

A tool that performs validation on PZP sites.

---

## Related Standards

### llms.txt

A proposed standard for a plain-text index file at `/llms.txt` that provides context for language models. PZP is compatible with but more comprehensive than llms.txt.

### AGENTS.md

A convention for documenting how AI coding agents should interact with a codebase. AGENTS.md focuses on development workflows; PZP focuses on content consumption.

### Model Context Protocol (MCP)

Anthropic's protocol for connecting language models to external tools and data sources. MCP handles tool execution; PZP handles web content structure.

### Schema.org

A vocabulary for structured data markup in HTML. PZP sites MAY use Schema.org concepts but express them in markdown and JSON rather than HTML microdata.

### RFC 2119

The IETF document defining requirements keywords (MUST, SHOULD, MAY, etc.) used throughout this specification.

---

## Abbreviations

| Abbreviation | Meaning |
|--------------|---------|
| API | Application Programming Interface |
| CORS | Cross-Origin Resource Sharing |
| CRUD | Create, Read, Update, Delete |
| DNS | Domain Name System |
| HTTP | Hypertext Transfer Protocol |
| HTTPS | HTTP Secure |
| JSON | JavaScript Object Notation |
| LLM | Large Language Model |
| MCP | Model Context Protocol |
| PZP | Prime Zero Protocol |
| RFC | Request for Comments |
| TXT | Text (as in DNS TXT record) |
| URL | Uniform Resource Locator |

---

*Terminology is the foundation of precision. Precision is the foundation of interoperability.*
