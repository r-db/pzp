# Context Specification

> Hierarchical context files provide efficient understanding at any depth.

## Overview

Context files are the core content mechanism of PZP. They provide agents with understanding of a site at multiple levels of detail, allowing efficient consumption based on token budgets and task requirements.

**Location:** `/context/` directory

**Format:** Markdown (`.md`)

---

## Context Hierarchy

PZP defines three levels of context:

```
┌─────────────────────────────────────────────────┐
│                 ESSENTIAL CONTEXT               │
│              /context/essential.md              │
│                  (~500 tokens)                  │
│                                                 │
│  "What is this site? What can I do here?"       │
└─────────────────────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────┐
│                   FULL CONTEXT                  │
│                /context/full.md                 │
│               (~2000-3000 tokens)               │
│                                                 │
│  "Complete understanding of capabilities"        │
└─────────────────────────────────────────────────┘
                        │
                        ▼
┌─────────────────────────────────────────────────┐
│                 TOPIC CONTEXTS                  │
│            /context/topics/{topic}.md           │
│              (~200-500 tokens each)             │
│                                                 │
│  "Deep dive on specific subjects"               │
└─────────────────────────────────────────────────┘
```

---

## Essential Context

### Requirements

- **Location:** `/context/essential.md`
- **Required:** SHOULD (strongly recommended)
- **Token budget:** 300-500 tokens
- **Purpose:** Quick relevance determination

### Content Structure

Essential context MUST answer:

1. **What is this?** — Site identity and purpose
2. **What can I do?** — Key capabilities
3. **How do I start?** — Primary actions or navigation

### Template

```markdown
# {Site Name} Essential Context

## What This Is

{2-3 sentences describing the site/service and its purpose}

## Key Capabilities

- {Capability 1}
- {Capability 2}
- {Capability 3}
- {Capability 4}

## Primary Actions

| Action | Endpoint | Description |
|--------|----------|-------------|
| {action-1} | {path} | {brief description} |
| {action-2} | {path} | {brief description} |
| {action-3} | {path} | {brief description} |

## Quick Links

- Full Context: [/context/full.md](/context/full.md)
- Actions: [/actions/index.json](/actions/index.json)
```

### Example

```markdown
# Acme Widgets Essential Context

## What This Is

Acme Widgets is an online retailer specializing in professional and consumer-grade widgets. We offer 500+ products across 12 categories with same-day shipping on most items.

## Key Capabilities

- Search products by keyword, category, price, availability
- View detailed product information with variants
- Add items to cart and complete checkout
- Track orders and manage returns

## Primary Actions

| Action | Endpoint | Description |
|--------|----------|-------------|
| search-products | GET /api/products/search | Search with filters |
| get-product | GET /api/products/{id} | Product details |
| add-to-cart | POST /api/cart/add | Add to cart |
| checkout | POST /api/checkout | Complete purchase |

## Constraints

- Ships to US and Canada only
- Max 10 items per order
- Authentication required for checkout
```

### Token Counting

Approximate token count: 1 word ≈ 1.3 tokens

| Section | Words | Tokens |
|---------|-------|--------|
| Header + What This Is | 50 | ~65 |
| Key Capabilities | 40 | ~52 |
| Primary Actions table | 60 | ~78 |
| Constraints | 25 | ~33 |
| **Total** | **175** | **~230** |

Essential context SHOULD stay under 400 tokens to leave room for formatting.

---

## Full Context

### Requirements

- **Location:** `/context/full.md`
- **Required:** SHOULD (strongly recommended)
- **Token budget:** 1500-3000 tokens
- **Purpose:** Complete site understanding

### Content Structure

Full context SHOULD include:

1. **Everything from essential context**
2. **Detailed capability descriptions**
3. **Complete action catalog with parameters**
4. **Policies and constraints**
5. **Common use cases and workflows**
6. **Error handling information**

### Template

```markdown
# {Site Name} Full Context

## Overview

{Detailed description of the site/service - 100-200 words}

## Complete Capabilities

### {Capability Category 1}

{Detailed description}

**Available operations:**
- {Operation 1}: {description}
- {Operation 2}: {description}

### {Capability Category 2}

{Detailed description}

## Actions Reference

### {action-name}

**Endpoint:** `{METHOD} {path}`
**Authentication:** {required/optional}

**Parameters:**
| Name | Type | Required | Description |
|------|------|----------|-------------|
| {param1} | {type} | {yes/no} | {description} |

**Response:**
{Description of response format}

### {next-action}
...

## Policies

### {Policy Type 1}
{Details}

### {Policy Type 2}
{Details}

## Common Workflows

### {Workflow 1}
1. {Step 1}
2. {Step 2}
3. {Step 3}

## Error Handling

| Error Code | Meaning | Resolution |
|------------|---------|------------|
| {code} | {meaning} | {how to resolve} |

## Support

{Contact and support information}
```

---

## Topic Contexts

### Requirements

- **Location:** `/context/topics/{topic}.md`
- **Required:** MAY
- **Token budget:** 200-500 tokens per topic
- **Purpose:** Deep dive on specific subjects

### When to Use

Topic contexts are useful when:
- Site has distinct functional areas
- Certain topics require detailed explanation
- Agents may need deep understanding of specific areas

### Common Topics

| Topic | Content |
|-------|---------|
| `shipping.md` | Shipping policies, methods, costs |
| `returns.md` | Return policies and procedures |
| `payment.md` | Payment methods and processing |
| `security.md` | Security practices and compliance |
| `api.md` | API usage and authentication |
| `pricing.md` | Pricing models and tiers |

### Example: shipping.md

```markdown
# Shipping Context

## Methods

| Service | Cost | Delivery | Tracking |
|---------|------|----------|----------|
| Standard | $5.99 | 5-7 days | Yes |
| Express | $12.99 | 2-3 days | Yes |
| Overnight | $24.99 | Next day | Yes |
| Free (orders $50+) | $0 | 5-7 days | Yes |

## Coverage

- **Continental US:** All methods available
- **Alaska/Hawaii:** Standard and Express only, +$10
- **Canada:** Standard only, $15 flat rate
- **International:** Not available

## Processing

- Orders before 2pm EST ship same day
- Weekend orders ship Monday
- Tracking email sent on shipment

## Restrictions

- Hazardous materials: ground only
- Oversized items: additional fees apply
- PO Boxes: Standard only
```

---

## Context Selection Logic

### Agent Decision Flow

```
Task: Agent needs to understand a PZP site

1. Read manifest.json
   └─ Get tokenBudgets, check available context

2. Assess token budget
   ├─ Plenty of tokens? → Read full.md
   ├─ Limited tokens? → Read essential.md
   └─ Very limited? → Use manifest description

3. Task requires specific topic?
   ├─ Yes → Read relevant topic context
   └─ No → Proceed with current understanding

4. Need more detail?
   └─ Read additional topic contexts as needed
```

### Efficient Reading

```python
# Agent implementation example
def get_context(site, task, available_tokens):
    manifest = fetch(f"{site}/manifest.json")
    budgets = manifest.get("tokenBudgets", {})

    # Start with essential
    if available_tokens >= budgets.get("essential", 500):
        context = fetch(f"{site}/context/essential.md")
        available_tokens -= budgets["essential"]
    else:
        return manifest["description"]  # Fallback

    # Add full if budget allows
    if available_tokens >= budgets.get("full", 2000):
        context += fetch(f"{site}/context/full.md")
        available_tokens -= budgets["full"]

    # Add relevant topics
    for topic in identify_relevant_topics(task):
        topic_budget = budgets.get("topic", 300)
        if available_tokens >= topic_budget:
            context += fetch(f"{site}/context/topics/{topic}.md")
            available_tokens -= topic_budget

    return context
```

---

## Writing Guidelines

### Be Concise

Every word should convey information. Avoid:
- Marketing language ("best-in-class", "revolutionary")
- Redundant phrases ("in order to", "the fact that")
- Obvious statements ("this is a website")

### Use Tables

Tables are more token-efficient than prose for structured data:

**Bad (verbose):**
```markdown
We offer standard shipping for $5.99 which takes 5-7 days.
Express shipping costs $12.99 and takes 2-3 days.
Overnight shipping is available for $24.99.
```

**Good (table):**
```markdown
| Service | Cost | Days |
|---------|------|------|
| Standard | $5.99 | 5-7 |
| Express | $12.99 | 2-3 |
| Overnight | $24.99 | 1 |
```

### Be Explicit

State facts directly. Avoid:
- Assumptions about agent knowledge
- References to visual elements
- Instructions that require human judgment

### Use Standard Markdown

Stick to widely-supported Markdown features:
- Headers (`#`, `##`, `###`)
- Lists (unordered `-`, ordered `1.`)
- Tables
- Code blocks
- Links
- Bold and italic

Avoid:
- Custom HTML
- Complex nested structures
- Images (unless critical and described)

---

## Validation

### Essential Context Checklist

- [ ] Under 500 tokens
- [ ] Answers "what is this?"
- [ ] Lists key capabilities
- [ ] Shows primary actions
- [ ] Links to full context

### Full Context Checklist

- [ ] Under 3000 tokens
- [ ] Includes all essential content
- [ ] Details all capabilities
- [ ] Documents all actions
- [ ] Explains policies
- [ ] Shows common workflows

### Token Verification

```bash
# Rough token estimate (words × 1.3)
wc -w context/essential.md
# Multiply result by 1.3 for token estimate
```

---

*Context is understanding. Hierarchy is efficiency.*
