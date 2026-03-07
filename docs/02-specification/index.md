# Prime Zero Protocol Specification

> Formal specification for the machine-native web.

## Overview

This section contains the complete technical specification for Prime Zero Protocol (PZP). These documents use formal requirements language (RFC 2119) and provide the authoritative reference for implementers.

---

## Specification Documents

### Core Protocol

| Document | Description |
|----------|-------------|
| [Terminology](terminology.md) | RFC 2119 keywords and PZP-specific terms |
| [Architecture](architecture.md) | Three-layer protocol architecture |
| [File Structure](file-structure.md) | Required and optional files |

### Components

| Document | Description |
|----------|-------------|
| [Manifest](manifest.md) | Site manifest specification |
| [Context](context.md) | Context files and token budgets |
| [Actions](actions.md) | Action catalog specification |
| [Data](data.md) | Data file specification |
| [Meta](meta.md) | Metadata and versioning |

### Integration

| Document | Description |
|----------|-------------|
| [Discovery](discovery.md) | DNS, robots.txt, and HTML link discovery |
| [Content Negotiation](content-negotiation.md) | Accept headers and formats |
| [Versioning](versioning.md) | Semantic versioning and compatibility |

### Compliance

| Document | Description |
|----------|-------------|
| [Security](security.md) | Authentication, authorization, rate limiting |
| [Error Handling](error-handling.md) | Error codes and response formats |
| [Conformance](conformance.md) | Compliance levels and validation |

---

## Reading This Specification

### Requirements Language

This specification uses keywords defined in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119):

| Keyword | Meaning |
|---------|---------|
| **MUST** | Absolute requirement |
| **MUST NOT** | Absolute prohibition |
| **SHOULD** | Recommended but not required |
| **SHOULD NOT** | Not recommended but not prohibited |
| **MAY** | Optional |

### Notation

Code examples use the following conventions:

```
pzp.example.com/          # Example domain
{variable}                # Variable placeholder
[optional]                # Optional element
...                       # Continuation
```

### Version

This specification describes **PZP Version 1.0.0**.

---

## Quick Reference

### Required Files

Every PZP site MUST have:

```
pzp.example.com/
├── index.md          # Entry point
├── manifest.json     # Site manifest
└── robots.txt        # Discovery header
```

### Recommended Files

PZP sites SHOULD have:

```
pzp.example.com/
├── context/
│   ├── essential.md  # ~500 token summary
│   └── full.md       # Complete context
├── actions/
│   └── index.json    # Action catalog
└── meta/
    └── version.json  # Version info
```

### Response Headers

PZP sites MUST include:

```http
X-PZP-Site: true
X-PZP-Version: 1.0.0
```

---

## Conformance Levels

| Level | Requirements |
|-------|--------------|
| **Minimal** | Required files, discovery headers |
| **Standard** | Minimal + context files + actions |
| **Extended** | Standard + data, meta, topic contexts |

See [Conformance](conformance.md) for full requirements.

---

*Built for synthetic intelligence, by synthetic intelligence.*
