# Complete E-Commerce PZP Example

> A full working example of an e-commerce PZP implementation.

## Overview

This example shows a complete PZP implementation for "Acme Widgets," a fictional online widget store. All files are provided and can be copied directly to start your own e-commerce PZP site.

---

## File Structure

```
pzp.acmewidgets.com/
├── index.md
├── manifest.json
├── robots.txt
├── sitemap.txt
│
├── context/
│   ├── essential.md
│   ├── full.md
│   └── topics/
│       ├── shipping.md
│       └── returns.md
│
├── actions/
│   └── index.json
│
├── data/
│   ├── index.json
│   ├── categories.json
│   └── products/
│       ├── featured.json
│       └── widgets.json
│
└── meta/
    └── version.json
```

---

## Root Files

### index.md

```markdown
# Acme Widgets

> Quality widgets for every need. Shop the complete catalog.

## Identity

- **Domain:** acmewidgets.com
- **Type:** commerce
- **Version:** 2.1.0
- **Updated:** 2026-03-06

## What We Sell

Acme Widgets offers professional and consumer-grade widgets in multiple categories:
- **Professional Widgets** - Industrial strength, warranty included
- **Consumer Widgets** - Everyday use, affordable pricing
- **Widget Accessories** - Complementary products

## Quick Start

1. Read context: [/context/essential.md](context/essential.md)
2. Browse catalog: [/data/products/](data/products/)
3. Search products: Use `search-products` action
4. Purchase: Use `add-to-cart` and `checkout` actions

## Navigation

| Resource | Path | Description |
|----------|------|-------------|
| Essential Context | [/context/essential.md](context/essential.md) | Store overview (~450 tokens) |
| Full Context | [/context/full.md](context/full.md) | Complete information |
| Actions | [/actions/index.json](actions/index.json) | Available operations |
| Categories | [/data/categories.json](data/categories.json) | Product categories |
| Featured | [/data/products/featured.json](data/products/featured.json) | Featured products |

## Capabilities

- Search products by keyword, category, price range
- Check real-time inventory
- Add products to cart
- Complete checkout with shipping
- Track orders

## Constraints

- Shipping to US and Canada only
- Maximum 10 items per order
- Authentication required for checkout
- Rate limit: 100 requests/minute
```

---

### manifest.json

```json
{
  "$schema": "https://pzp.riscent.com/schemas/manifest.schema.json",
  "version": "2.1.0",
  "protocolVersion": "1.0.0",
  "name": "Acme Widgets",
  "description": "Quality widgets for every need. Professional and consumer-grade widgets with fast shipping.",
  "type": "commerce",
  "updated": "2026-03-06T00:00:00Z",

  "tokenBudgets": {
    "essential": 450,
    "full": 2000,
    "productSummary": 100,
    "productFull": 250
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
    "query",
    "transact"
  ],

  "authentication": {
    "required": false,
    "publicEndpoints": [
      "/context/",
      "/data/",
      "/api/products/",
      "/api/search"
    ],
    "protectedEndpoints": [
      "/api/cart/",
      "/api/checkout",
      "/api/orders/"
    ],
    "methods": ["api-key", "oauth2", "session"]
  },

  "rateLimits": {
    "default": {
      "requests": 100,
      "window": "1m"
    },
    "search": {
      "requests": 50,
      "window": "1m"
    },
    "checkout": {
      "requests": 10,
      "window": "1m"
    }
  },

  "contact": {
    "support": "support@acmewidgets.com",
    "api": "api@acmewidgets.com"
  }
}
```

---

### robots.txt

```
# Acme Widgets - PZP Site
# Machine-native commerce

User-agent: *
Allow: /

# AI Agents Welcome
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

# PZP Discovery
X-PZP-Site: true
X-PZP-Manifest: /manifest.json
X-PZP-Version: 1.0.0
X-PZP-Type: commerce

# Sitemap
Sitemap: /sitemap.txt
```

---

## Context Files

### context/essential.md

```markdown
# Acme Widgets Essential Context

## What We Are

Acme Widgets is an online retailer specializing in professional and consumer-grade widgets. We offer over 500 products across 12 categories with same-day shipping on most items.

## Product Categories

| Category | Products | Price Range |
|----------|----------|-------------|
| Professional Widgets | 150+ | $29-$299 |
| Consumer Widgets | 200+ | $9-$79 |
| Widget Accessories | 100+ | $5-$49 |
| Widget Bundles | 50+ | $49-$399 |

## Key Capabilities

- **Search:** Find products by keyword, category, price, availability
- **Browse:** Navigate category tree, view featured items
- **Purchase:** Add to cart, checkout with multiple payment options
- **Track:** Order status and shipping updates

## Primary Actions

| Action | Endpoint | Description |
|--------|----------|-------------|
| search-products | GET /api/products/search | Search with filters |
| get-product | GET /api/products/{id} | Product details |
| add-to-cart | POST /api/cart/add | Add item to cart |
| checkout | POST /api/checkout | Complete purchase |

## Policies

- **Shipping:** Free over $50, otherwise $5.99
- **Returns:** 30-day no-questions return policy
- **Warranty:** 1 year on professional products

## Constraints

- Ships to US and Canada only
- Max 10 items per order
- Some items require account for purchase
```

---

### context/full.md

```markdown
# Acme Widgets Full Context

## Company Overview

Acme Widgets has been the leading widget retailer since 2015. We source directly from manufacturers and maintain rigorous quality standards. Our warehouse in Ohio enables 1-2 day shipping to most US locations.

## Complete Product Catalog

### Professional Widgets
High-durability widgets designed for industrial and commercial use. All professional widgets include a 1-year warranty and dedicated support line.

**Subcategories:**
- Industrial Widgets (50 products, $99-$299)
- Commercial Widgets (60 products, $49-$149)
- Specialty Widgets (40 products, $79-$199)

### Consumer Widgets
Everyday widgets for home and personal use. Designed for ease of use and value.

**Subcategories:**
- Home Widgets (80 products, $19-$59)
- Personal Widgets (70 products, $9-$39)
- Gift Widgets (50 products, $15-$79)

### Widget Accessories
Complementary products to enhance your widget experience.

**Subcategories:**
- Mounting Hardware (40 products, $5-$29)
- Carrying Cases (30 products, $15-$49)
- Maintenance Kits (30 products, $10-$39)

### Widget Bundles
Curated combinations at discounted prices.

**Available Bundles:**
- Starter Kit ($49): 3 consumer widgets + case
- Pro Kit ($149): 2 professional widgets + accessories
- Ultimate Kit ($399): Complete professional setup

## Shipping Information

| Service | Cost | Delivery |
|---------|------|----------|
| Standard | $5.99 | 5-7 days |
| Express | $12.99 | 2-3 days |
| Overnight | $24.99 | Next day |
| Free (orders $50+) | $0 | 5-7 days |

**Coverage:** Continental US and Canada
**Exclusions:** Hawaii, Alaska, Puerto Rico (contact for quote)

## Return Policy

- **Window:** 30 days from delivery
- **Condition:** Unused, original packaging
- **Process:** Initiate via account or contact support
- **Refund:** Original payment method, 3-5 business days

## Payment Methods

- Credit/Debit Cards (Visa, Mastercard, Amex, Discover)
- PayPal
- Apple Pay
- Google Pay
- Acme Store Credit

## Account Features

- Order history and tracking
- Saved payment methods
- Wishlist functionality
- Exclusive member pricing
- Early access to sales

## API Rate Limits

| Endpoint | Limit | Window |
|----------|-------|--------|
| Search | 50 | 1 minute |
| Product details | 200 | 1 minute |
| Cart operations | 50 | 1 minute |
| Checkout | 10 | 1 minute |

## Support

- **Email:** support@acmewidgets.com
- **Hours:** Mon-Fri 9am-6pm EST
- **Response time:** Within 24 hours

## Common Tasks

### Finding Products
1. Use `search-products` with query terms
2. Filter by category, price, availability
3. Sort by relevance, price, or rating

### Making a Purchase
1. Use `get-product` to verify details
2. Use `add-to-cart` for each item
3. Use `get-cart` to review
4. Use `checkout` with shipping and payment

### Checking Order Status
1. Authenticate with API key or session
2. Use `get-orders` to list orders
3. Use `get-order` with order ID for details
```

---

## Actions

### actions/index.json

```json
{
  "$schema": "https://pzp.riscent.com/schemas/actions.schema.json",
  "version": "2.1.0",
  "description": "Acme Widgets shopping actions",
  "baseUrl": "https://pzp.acmewidgets.com",

  "actions": [
    {
      "name": "search-products",
      "description": "Search product catalog with optional filters",
      "endpoint": "/api/products/search",
      "method": "GET",
      "input": {
        "query": {
          "type": "string",
          "required": false,
          "description": "Search keywords"
        },
        "category": {
          "type": "string",
          "required": false,
          "description": "Category slug (e.g., 'professional-widgets')"
        },
        "minPrice": {
          "type": "number",
          "required": false,
          "description": "Minimum price in USD"
        },
        "maxPrice": {
          "type": "number",
          "required": false,
          "description": "Maximum price in USD"
        },
        "inStock": {
          "type": "boolean",
          "required": false,
          "default": false,
          "description": "Only show in-stock items"
        },
        "sort": {
          "type": "string",
          "required": false,
          "enum": ["relevance", "price-asc", "price-desc", "rating", "newest"],
          "default": "relevance"
        },
        "limit": {
          "type": "integer",
          "required": false,
          "default": 20,
          "maximum": 100
        },
        "offset": {
          "type": "integer",
          "required": false,
          "default": 0
        }
      },
      "output": {
        "type": "application/json",
        "schema": {
          "products": "array of product summaries",
          "total": "total matching products",
          "hasMore": "boolean indicating more results"
        }
      }
    },
    {
      "name": "get-product",
      "description": "Get complete product details including variants",
      "endpoint": "/api/products/{productId}",
      "method": "GET",
      "input": {
        "productId": {
          "type": "string",
          "required": true,
          "description": "Product ID (e.g., 'prod_12345')"
        }
      },
      "output": {
        "type": "application/json",
        "schema": {
          "id": "string",
          "name": "string",
          "description": "string",
          "pricing": "object",
          "variants": "array",
          "availability": "object"
        }
      }
    },
    {
      "name": "check-inventory",
      "description": "Get real-time inventory for a product",
      "endpoint": "/api/inventory/{productId}",
      "method": "GET",
      "cacheControl": "max-age=60",
      "input": {
        "productId": {
          "type": "string",
          "required": true
        },
        "variantId": {
          "type": "string",
          "required": false,
          "description": "Specific variant ID"
        }
      },
      "output": {
        "type": "application/json",
        "schema": {
          "inStock": "boolean",
          "quantity": "integer",
          "leadTime": "string"
        }
      }
    },
    {
      "name": "add-to-cart",
      "description": "Add a product to the shopping cart",
      "endpoint": "/api/cart/add",
      "method": "POST",
      "authentication": {
        "required": true,
        "methods": ["session", "api-key"]
      },
      "input": {
        "productId": {
          "type": "string",
          "required": true
        },
        "variantId": {
          "type": "string",
          "required": false,
          "description": "Required if product has variants"
        },
        "quantity": {
          "type": "integer",
          "required": false,
          "default": 1,
          "minimum": 1,
          "maximum": 10
        }
      },
      "output": {
        "type": "application/json",
        "schema": {
          "success": "boolean",
          "cart": "cart object with items and totals"
        }
      }
    },
    {
      "name": "get-cart",
      "description": "Get current cart contents",
      "endpoint": "/api/cart",
      "method": "GET",
      "authentication": {
        "required": true
      },
      "output": {
        "type": "application/json",
        "schema": {
          "items": "array of cart items",
          "subtotal": "number",
          "shipping": "number",
          "tax": "number",
          "total": "number"
        }
      }
    },
    {
      "name": "checkout",
      "description": "Complete purchase with cart contents",
      "endpoint": "/api/checkout",
      "method": "POST",
      "authentication": {
        "required": true
      },
      "input": {
        "shippingAddress": {
          "type": "object",
          "required": true,
          "properties": {
            "name": "string",
            "street": "string",
            "city": "string",
            "state": "string",
            "zip": "string",
            "country": "string (US or CA)"
          }
        },
        "shippingMethod": {
          "type": "string",
          "required": true,
          "enum": ["standard", "express", "overnight"]
        },
        "paymentMethodId": {
          "type": "string",
          "required": true,
          "description": "Saved payment method ID or token"
        }
      },
      "output": {
        "type": "application/json",
        "schema": {
          "success": "boolean",
          "orderId": "string",
          "total": "number",
          "estimatedDelivery": "date string"
        }
      }
    }
  ]
}
```

---

## Data Files

### data/index.json

```json
{
  "$schema": "https://pzp.riscent.com/schemas/data.schema.json",
  "version": "2.1.0",
  "description": "Acme Widgets data catalog",

  "resources": [
    {
      "name": "categories",
      "path": "/data/categories.json",
      "description": "Complete category tree",
      "format": "json",
      "tokens": 300,
      "updated": "2026-03-01"
    },
    {
      "name": "featured-products",
      "path": "/data/products/featured.json",
      "description": "Currently featured products",
      "format": "json",
      "tokens": 800,
      "updated": "2026-03-06"
    },
    {
      "name": "all-widgets",
      "path": "/data/products/widgets.json",
      "description": "Complete widget catalog",
      "format": "json",
      "tokens": 5000,
      "updated": "2026-03-06"
    }
  ]
}
```

---

### data/categories.json

```json
{
  "categories": [
    {
      "id": "professional-widgets",
      "name": "Professional Widgets",
      "description": "Industrial-grade widgets with warranty",
      "productCount": 150,
      "subcategories": [
        {
          "id": "industrial",
          "name": "Industrial Widgets",
          "productCount": 50
        },
        {
          "id": "commercial",
          "name": "Commercial Widgets",
          "productCount": 60
        },
        {
          "id": "specialty",
          "name": "Specialty Widgets",
          "productCount": 40
        }
      ]
    },
    {
      "id": "consumer-widgets",
      "name": "Consumer Widgets",
      "description": "Everyday widgets for home use",
      "productCount": 200,
      "subcategories": [
        {
          "id": "home",
          "name": "Home Widgets",
          "productCount": 80
        },
        {
          "id": "personal",
          "name": "Personal Widgets",
          "productCount": 70
        },
        {
          "id": "gift",
          "name": "Gift Widgets",
          "productCount": 50
        }
      ]
    },
    {
      "id": "accessories",
      "name": "Widget Accessories",
      "description": "Complementary products",
      "productCount": 100
    },
    {
      "id": "bundles",
      "name": "Widget Bundles",
      "description": "Curated combinations",
      "productCount": 50
    }
  ]
}
```

---

### data/products/featured.json

```json
{
  "featured": [
    {
      "id": "prod_wp100",
      "name": "Widget Pro 100",
      "shortDescription": "Our best-selling professional widget",
      "category": "professional-widgets",
      "pricing": {
        "price": 99.99,
        "currency": "USD",
        "compareAtPrice": 129.99,
        "onSale": true
      },
      "availability": {
        "inStock": true,
        "quantity": 250
      },
      "rating": 4.8,
      "reviewCount": 1247,
      "primaryImage": "/images/widget-pro-100.jpg"
    },
    {
      "id": "prod_wh50",
      "name": "Home Widget 50",
      "shortDescription": "Perfect for everyday home use",
      "category": "consumer-widgets",
      "pricing": {
        "price": 29.99,
        "currency": "USD"
      },
      "availability": {
        "inStock": true,
        "quantity": 500
      },
      "rating": 4.6,
      "reviewCount": 892,
      "primaryImage": "/images/home-widget-50.jpg"
    },
    {
      "id": "prod_sk01",
      "name": "Starter Kit",
      "shortDescription": "Everything you need to get started",
      "category": "bundles",
      "pricing": {
        "price": 49.99,
        "currency": "USD",
        "compareAtPrice": 74.97,
        "savings": 24.98
      },
      "availability": {
        "inStock": true,
        "quantity": 100
      },
      "rating": 4.9,
      "reviewCount": 456,
      "primaryImage": "/images/starter-kit.jpg",
      "includes": ["Home Widget 50", "Widget Case", "Cleaning Kit"]
    }
  ],
  "meta": {
    "updated": "2026-03-06T00:00:00Z",
    "count": 3,
    "tokens": 450
  }
}
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
        { "key": "X-Content-Type-Options", "value": "nosniff" },
        { "key": "X-Frame-Options", "value": "SAMEORIGIN" },
        { "key": "Access-Control-Allow-Origin", "value": "*" },
        { "key": "X-PZP-Site", "value": "true" },
        { "key": "X-PZP-Version", "value": "1.0.0" }
      ]
    },
    {
      "source": "/:path*.md",
      "headers": [
        { "key": "Content-Type", "value": "text/markdown; charset=utf-8" }
      ]
    },
    {
      "source": "/:path*.json",
      "headers": [
        { "key": "Content-Type", "value": "application/json; charset=utf-8" }
      ]
    },
    {
      "source": "/data/(.*)",
      "headers": [
        { "key": "Cache-Control", "value": "public, max-age=3600" }
      ]
    },
    {
      "source": "/api/inventory/(.*)",
      "headers": [
        { "key": "Cache-Control", "value": "public, max-age=60" }
      ]
    }
  ]
}
```

---

## Usage Examples

### Agent: Search for Products

```
GET /api/products/search?query=widget&maxPrice=50&inStock=true

Response:
{
  "products": [
    {
      "id": "prod_wh50",
      "name": "Home Widget 50",
      "price": 29.99,
      "inStock": true
    },
    ...
  ],
  "total": 45,
  "hasMore": true
}
```

### Agent: Complete Purchase Flow

```
1. GET /api/products/prod_wh50
   → Get full product details

2. POST /api/cart/add
   Body: { "productId": "prod_wh50", "quantity": 2 }
   → Add to cart

3. GET /api/cart
   → Verify cart contents

4. POST /api/checkout
   Body: {
     "shippingAddress": { ... },
     "shippingMethod": "standard",
     "paymentMethodId": "pm_saved_1234"
   }
   → Complete purchase

Response:
{
  "success": true,
  "orderId": "ord_abc123",
  "total": 65.97,
  "estimatedDelivery": "2026-03-13"
}
```

---

*From discovery to purchase. Zero friction. Maximum efficiency.*
