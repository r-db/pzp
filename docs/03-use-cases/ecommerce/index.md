# E-Commerce PZP Implementation

> How to implement PZP for online stores, product catalogs, and shopping experiences.

## Overview

E-commerce is one of the most valuable PZP use cases. AI shopping assistants need to:
- Discover and search products
- Check prices and availability
- Compare options
- Complete purchases

Without PZP, agents scrape HTML, parse JavaScript-rendered catalogs, and guess at checkout flows. With PZP, all of this becomes structured, efficient, and reliable.

---

## Why E-Commerce Needs PZP

### The Current Problem

When a user asks "find me a blue widget under $50":

1. Agent searches the web for widget stores
2. Agent fetches product pages (HTML + JS)
3. Agent tries to parse product information:
   - Price (where is it? what format?)
   - Color (attribute? variant? description?)
   - Availability (in stock? shipping time?)
4. Agent compares across stores
5. Agent presents options (hoping data is correct)

**Failure modes:**
- JavaScript-rendered catalogs return empty
- Prices parsed incorrectly (sale price vs. original)
- Variants confused (size, color, material)
- Availability stale or wrong
- Checkout impossible without human

### The PZP Solution

With PZP, the same request:

1. Agent queries PZP site: `/api/search?q=widget&color=blue&maxPrice=50`
2. Agent receives structured JSON with:
   - Product name, description, price
   - All variants with availability
   - Purchase action endpoint
3. Agent compares across PZP-enabled stores
4. Agent can execute purchase action directly

**Result:** Accurate data, efficient retrieval, agent-executable transactions.

---

## E-Commerce PZP Structure

```
pzp.shop.example.com/
├── index.md                    # Store entry point
├── manifest.json               # Store manifest
├── robots.txt                  # Discovery
│
├── context/
│   ├── essential.md            # Store overview (~500 tokens)
│   ├── full.md                 # Complete store context
│   └── topics/
│       ├── shipping.md         # Shipping policies
│       ├── returns.md          # Return policies
│       └── payment.md          # Payment methods
│
├── actions/
│   └── index.json              # Shopping actions
│
├── data/
│   ├── index.json              # Data catalog
│   ├── categories.json         # Category tree
│   └── products/
│       ├── featured.json       # Featured products
│       └── {category}.json     # Products by category
│
└── meta/
    └── version.json            # Version info
```

---

## Documents in This Section

| Document | Description |
|----------|-------------|
| [Product Catalog](product-catalog.md) | How to expose product data |
| [Search](search.md) | Product search implementation |
| [Checkout](checkout.md) | Purchase flow actions |
| [Inventory](inventory.md) | Real-time availability |
| [Recommendations](recommendations.md) | AI-powered product suggestions |
| [Complete Example](example-complete.md) | Full e-commerce PZP site |

---

## Key Actions for E-Commerce

### Product Discovery

```json
{
  "name": "search-products",
  "description": "Search product catalog with filters",
  "endpoint": "/api/products/search",
  "method": "GET",
  "input": {
    "query": { "type": "string", "description": "Search terms" },
    "category": { "type": "string", "description": "Category filter" },
    "minPrice": { "type": "number", "description": "Minimum price" },
    "maxPrice": { "type": "number", "description": "Maximum price" },
    "inStock": { "type": "boolean", "description": "Only in-stock items" }
  }
}
```

### Product Details

```json
{
  "name": "get-product",
  "description": "Get detailed product information",
  "endpoint": "/api/products/{productId}",
  "method": "GET",
  "input": {
    "productId": { "type": "string", "required": true }
  }
}
```

### Add to Cart

```json
{
  "name": "add-to-cart",
  "description": "Add product to shopping cart",
  "endpoint": "/api/cart/add",
  "method": "POST",
  "input": {
    "productId": { "type": "string", "required": true },
    "quantity": { "type": "integer", "default": 1 },
    "variantId": { "type": "string", "description": "Specific variant" }
  },
  "authentication": {
    "required": true,
    "methods": ["session", "api-key"]
  }
}
```

### Checkout

```json
{
  "name": "checkout",
  "description": "Complete purchase with cart contents",
  "endpoint": "/api/checkout",
  "method": "POST",
  "input": {
    "shippingAddress": { "type": "object", "required": true },
    "paymentMethod": { "type": "string", "required": true }
  },
  "authentication": {
    "required": true
  }
}
```

---

## Product Data Schema

### Product Object

```json
{
  "id": "prod_12345",
  "name": "Premium Widget Pro",
  "description": "The best widget for professionals",
  "shortDescription": "Professional-grade widget",
  "category": "widgets",
  "subcategory": "professional",

  "pricing": {
    "price": 49.99,
    "currency": "USD",
    "compareAtPrice": 69.99,
    "onSale": true
  },

  "availability": {
    "inStock": true,
    "quantity": 150,
    "leadTime": "1-2 days",
    "backorderAllowed": false
  },

  "variants": [
    {
      "id": "var_blue_small",
      "name": "Blue / Small",
      "attributes": {
        "color": "blue",
        "size": "small"
      },
      "price": 49.99,
      "inStock": true,
      "sku": "WP-BLU-SM"
    }
  ],

  "media": {
    "primaryImage": "/images/widget-pro-main.jpg",
    "images": ["/images/widget-pro-1.jpg", "/images/widget-pro-2.jpg"]
  },

  "attributes": {
    "material": "aluminum",
    "weight": "250g",
    "dimensions": "10x5x3 cm"
  },

  "metadata": {
    "created": "2026-01-15T00:00:00Z",
    "updated": "2026-03-01T00:00:00Z",
    "tokens": 150
  }
}
```

---

## Token Budget Guidelines

| Content | Budget | Notes |
|---------|--------|-------|
| Essential context | 400-500 | Store overview, categories, key policies |
| Product summary | 50-100 | Name, price, availability |
| Product full | 150-250 | All details including variants |
| Category listing | 500-1000 | 10-20 products with summaries |
| Search results | 300-500 | 5-10 matching products |

---

## Integration Patterns

### Static Product Data

For stores with infrequent catalog changes:

```
/data/products/all.json         # Complete catalog (cached)
/data/products/electronics.json # By category
```

**Pros:** Simple, cacheable, no API needed
**Cons:** Stale data, large files

### Dynamic API

For stores with real-time inventory:

```
/api/products/search            # Search endpoint
/api/products/{id}              # Product endpoint
/api/inventory/{id}             # Live availability
```

**Pros:** Real-time data, filtered queries
**Cons:** Requires backend, rate limiting needed

### Hybrid Approach

Best of both:

```
/data/products/catalog.json     # Static catalog (daily update)
/api/inventory/check            # Real-time stock check
/api/cart/*                     # Dynamic cart/checkout
```

---

## Security Considerations

### Public vs. Protected Actions

| Action | Authentication |
|--------|----------------|
| Search products | Public |
| View product | Public |
| Check inventory | Public |
| Add to cart | Session required |
| Checkout | Authentication required |
| View orders | Authentication required |

### Rate Limiting

Recommended limits:

```json
{
  "rateLimits": {
    "search": { "requests": 100, "window": "1m" },
    "product": { "requests": 500, "window": "1m" },
    "cart": { "requests": 50, "window": "1m" },
    "checkout": { "requests": 10, "window": "1m" }
  }
}
```

### Data Privacy

- Never expose customer data in public endpoints
- Sanitize product descriptions (no PII)
- Consider geographic restrictions
- Implement proper authentication for transactions

---

## Common Challenges

### Variant Explosion

Products with many variants (size × color × material) can explode token counts.

**Solution:** Summary endpoint returns base product + variant count; detail endpoint returns full variants on request.

### Price Complexity

Sales, member pricing, tiered pricing, bundles.

**Solution:** Return all applicable prices with clear labels:

```json
{
  "pricing": {
    "basePrice": 99.99,
    "salePrice": 79.99,
    "memberPrice": 69.99,
    "applicablePrice": 79.99,
    "priceNote": "Sale ends March 15"
  }
}
```

### Real-Time Inventory

Stock changes constantly.

**Solution:** Separate inventory endpoint with short cache TTL:

```json
{
  "name": "check-inventory",
  "endpoint": "/api/inventory/{productId}",
  "cacheControl": "max-age=60"
}
```

---

## Success Metrics

Track PZP adoption effectiveness:

| Metric | Description |
|--------|-------------|
| Agent requests/day | Volume of PZP traffic |
| Conversion rate | PZP sessions that lead to purchase |
| Token efficiency | Average tokens per transaction |
| Error rate | Failed actions / total actions |
| Response time | P95 latency for PZP endpoints |

---

*E-commerce without friction. Shopping without scraping.*
