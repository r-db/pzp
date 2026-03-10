# Actions Specification

> Actions define what agents can do, not just what they can read.

## Overview

Actions are the executable operations on a PZP site. They transform PZP from passive content into interactive capability. Well-defined actions enable agents to complete tasks autonomously.

**Location:** `/actions/index.json`

**Content-Type:** `application/json; charset=utf-8`

---

## Schema

The actions catalog MUST validate against the PZP actions schema:

```
$schema: https://pzp.riscent.com/schemas/actions.schema.json
```

---

## Actions Catalog Structure

```json
{
  "$schema": "https://pzp.riscent.com/schemas/actions.schema.json",
  "version": "1.0.0",
  "description": "Available actions on this site",
  "baseUrl": "https://pzp.example.com",
  "actions": [
    { /* action definition */ },
    { /* action definition */ }
  ]
}
```

### Root Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `$schema` | string | SHOULD | Schema URL for validation |
| `version` | string | MUST | Catalog version (semver) |
| `description` | string | SHOULD | Catalog description |
| `baseUrl` | string | MUST | Base URL for endpoints |
| `actions` | array | MUST | Array of action definitions |

---

## Action Definition

Each action in the `actions` array MUST have:

```json
{
  "name": "search-products",
  "description": "Search product catalog with filters",
  "endpoint": "/api/products/search",
  "method": "GET",
  "input": { /* input schema */ },
  "output": { /* output schema */ }
}
```

### Required Fields

#### name

**Type:** `string`
**Required:** MUST

Unique identifier for this action. Use kebab-case.

```json
{
  "name": "search-products"
}
```

**Naming conventions:**
- Verb-noun format: `search-products`, `get-user`, `create-order`
- Lowercase with hyphens
- Descriptive but concise

#### description

**Type:** `string`
**Required:** MUST

Clear description of what the action does.

```json
{
  "description": "Search product catalog with optional filters for category, price range, and availability"
}
```

**Guidelines:**
- One sentence, under 100 characters
- State what it does, not how
- Include key parameters if space allows

#### endpoint

**Type:** `string`
**Required:** MUST

URL path for the action (appended to `baseUrl`).

```json
{
  "endpoint": "/api/products/search"
}
```

**Path parameters:**
```json
{
  "endpoint": "/api/products/{productId}"
}
```

Path parameters use `{paramName}` syntax and MUST be defined in `input`.

#### method

**Type:** `string`
**Required:** MUST

HTTP method for the action.

```json
{
  "method": "GET"
}
```

**Valid methods:**

| Method | Use Case |
|--------|----------|
| `GET` | Retrieve data, search, query |
| `POST` | Create resource, execute action |
| `PUT` | Replace resource |
| `PATCH` | Partial update |
| `DELETE` | Remove resource |

#### input

**Type:** `object`
**Required:** MUST (may be empty `{}`)

Schema for action parameters.

```json
{
  "input": {
    "query": {
      "type": "string",
      "required": false,
      "description": "Search keywords"
    },
    "maxPrice": {
      "type": "number",
      "required": false,
      "description": "Maximum price in USD"
    }
  }
}
```

See [Input Schema](#input-schema) for full specification.

### Optional Fields

#### output

**Type:** `object`
**Required:** SHOULD

Description of the response format.

```json
{
  "output": {
    "type": "application/json",
    "schema": {
      "products": "array of product objects",
      "total": "number of matching products",
      "hasMore": "boolean indicating pagination"
    }
  }
}
```

#### authentication

**Type:** `object`
**Required:** SHOULD (if auth needed)

Authentication requirements for this action.

```json
{
  "authentication": {
    "required": true,
    "methods": ["api-key", "oauth2"]
  }
}
```

#### rateLimit

**Type:** `object`
**Required:** MAY

Rate limiting for this specific action.

```json
{
  "rateLimit": {
    "requests": 50,
    "window": "1m"
  }
}
```

#### cacheControl

**Type:** `string`
**Required:** MAY

Cache-Control header for responses.

```json
{
  "cacheControl": "max-age=60"
}
```

#### deprecated

**Type:** `boolean`
**Required:** MAY

Whether this action is deprecated.

```json
{
  "deprecated": true,
  "deprecationNote": "Use search-products-v2 instead"
}
```

---

## Input Schema

### Parameter Definition

Each input parameter is defined with:

```json
{
  "paramName": {
    "type": "string",
    "required": true,
    "description": "What this parameter is for",
    "default": "defaultValue",
    "enum": ["option1", "option2"],
    "minimum": 0,
    "maximum": 100,
    "pattern": "^[a-z]+$"
  }
}
```

### Parameter Fields

| Field | Type | Description |
|-------|------|-------------|
| `type` | string | Data type (see below) |
| `required` | boolean | Whether required (default: false) |
| `description` | string | Parameter description |
| `default` | any | Default value if not provided |
| `enum` | array | Allowed values |
| `minimum` | number | Minimum value (numbers) |
| `maximum` | number | Maximum value (numbers) |
| `minLength` | integer | Minimum length (strings) |
| `maxLength` | integer | Maximum length (strings) |
| `pattern` | string | Regex pattern (strings) |
| `items` | object | Item schema (arrays) |
| `properties` | object | Property schemas (objects) |

### Data Types

| Type | Description | Example |
|------|-------------|---------|
| `string` | Text value | `"hello"` |
| `number` | Numeric value | `42.5` |
| `integer` | Whole number | `42` |
| `boolean` | True/false | `true` |
| `array` | List of values | `[1, 2, 3]` |
| `object` | Structured data | `{"key": "value"}` |

### Examples

**String with enum:**
```json
{
  "sort": {
    "type": "string",
    "required": false,
    "enum": ["relevance", "price-asc", "price-desc", "rating"],
    "default": "relevance",
    "description": "Sort order for results"
  }
}
```

**Number with range:**
```json
{
  "limit": {
    "type": "integer",
    "required": false,
    "minimum": 1,
    "maximum": 100,
    "default": 20,
    "description": "Number of results to return"
  }
}
```

**Object parameter:**
```json
{
  "shippingAddress": {
    "type": "object",
    "required": true,
    "description": "Delivery address",
    "properties": {
      "name": { "type": "string", "required": true },
      "street": { "type": "string", "required": true },
      "city": { "type": "string", "required": true },
      "state": { "type": "string", "required": true },
      "zip": { "type": "string", "required": true },
      "country": { "type": "string", "required": true, "enum": ["US", "CA"] }
    }
  }
}
```

**Array parameter:**
```json
{
  "productIds": {
    "type": "array",
    "required": true,
    "description": "List of product IDs to add",
    "items": {
      "type": "string"
    },
    "minItems": 1,
    "maxItems": 10
  }
}
```

---

## Output Schema

### Basic Output

```json
{
  "output": {
    "type": "application/json",
    "description": "Search results with pagination",
    "schema": {
      "products": "array of product summaries",
      "total": "integer count of all matches",
      "hasMore": "boolean for pagination"
    }
  }
}
```

### Detailed Output

```json
{
  "output": {
    "type": "application/json",
    "schema": {
      "success": {
        "type": "boolean",
        "description": "Whether the action succeeded"
      },
      "data": {
        "type": "object",
        "description": "Response payload",
        "properties": {
          "products": {
            "type": "array",
            "items": {
              "id": "string",
              "name": "string",
              "price": "number"
            }
          }
        }
      },
      "meta": {
        "type": "object",
        "properties": {
          "total": "integer",
          "page": "integer",
          "hasMore": "boolean"
        }
      }
    }
  }
}
```

---

## Common Action Patterns

### Search/Query

```json
{
  "name": "search",
  "description": "Search with filters and pagination",
  "endpoint": "/api/search",
  "method": "GET",
  "input": {
    "query": { "type": "string", "description": "Search terms" },
    "filters": { "type": "object", "description": "Filter criteria" },
    "sort": { "type": "string", "enum": ["relevance", "date", "name"] },
    "limit": { "type": "integer", "default": 20, "maximum": 100 },
    "offset": { "type": "integer", "default": 0 }
  },
  "output": {
    "type": "application/json",
    "schema": {
      "results": "array",
      "total": "integer",
      "hasMore": "boolean"
    }
  }
}
```

### Get Resource

```json
{
  "name": "get-resource",
  "description": "Retrieve a specific resource by ID",
  "endpoint": "/api/resources/{resourceId}",
  "method": "GET",
  "input": {
    "resourceId": {
      "type": "string",
      "required": true,
      "description": "Resource identifier"
    }
  },
  "output": {
    "type": "application/json",
    "schema": {
      "resource": "complete resource object"
    }
  }
}
```

### Create Resource

```json
{
  "name": "create-resource",
  "description": "Create a new resource",
  "endpoint": "/api/resources",
  "method": "POST",
  "authentication": { "required": true },
  "input": {
    "name": { "type": "string", "required": true },
    "data": { "type": "object", "required": true }
  },
  "output": {
    "type": "application/json",
    "schema": {
      "success": "boolean",
      "resource": "created resource with ID"
    }
  }
}
```

### Execute Transaction

```json
{
  "name": "checkout",
  "description": "Complete a purchase transaction",
  "endpoint": "/api/checkout",
  "method": "POST",
  "authentication": { "required": true },
  "rateLimit": { "requests": 10, "window": "1m" },
  "input": {
    "cartId": { "type": "string", "required": true },
    "paymentMethodId": { "type": "string", "required": true },
    "shippingAddress": { "type": "object", "required": true }
  },
  "output": {
    "type": "application/json",
    "schema": {
      "success": "boolean",
      "orderId": "string",
      "total": "number",
      "estimatedDelivery": "ISO date string"
    }
  }
}
```

---

## Complete Example

```json
{
  "$schema": "https://pzp.riscent.com/schemas/actions.schema.json",
  "version": "2.1.0",
  "description": "Acme Widget Store actions",
  "baseUrl": "https://pzp.acmewidgets.com",

  "actions": [
    {
      "name": "search-products",
      "description": "Search product catalog with filters",
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
          "description": "Category slug"
        },
        "minPrice": {
          "type": "number",
          "required": false,
          "minimum": 0,
          "description": "Minimum price USD"
        },
        "maxPrice": {
          "type": "number",
          "required": false,
          "description": "Maximum price USD"
        },
        "inStock": {
          "type": "boolean",
          "required": false,
          "default": false,
          "description": "Only in-stock items"
        },
        "sort": {
          "type": "string",
          "required": false,
          "enum": ["relevance", "price-asc", "price-desc", "rating"],
          "default": "relevance"
        },
        "limit": {
          "type": "integer",
          "required": false,
          "default": 20,
          "minimum": 1,
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
          "total": "total matching count",
          "hasMore": "boolean for pagination"
        }
      },
      "cacheControl": "max-age=300"
    },

    {
      "name": "get-product",
      "description": "Get detailed product information",
      "endpoint": "/api/products/{productId}",
      "method": "GET",
      "input": {
        "productId": {
          "type": "string",
          "required": true,
          "description": "Product ID"
        }
      },
      "output": {
        "type": "application/json",
        "schema": {
          "id": "string",
          "name": "string",
          "description": "string",
          "pricing": "object with price, salePrice, currency",
          "variants": "array of variant objects",
          "availability": "object with inStock, quantity"
        }
      },
      "cacheControl": "max-age=60"
    },

    {
      "name": "add-to-cart",
      "description": "Add product to shopping cart",
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
          "cart": "updated cart object"
        }
      },
      "rateLimit": {
        "requests": 50,
        "window": "1m"
      }
    },

    {
      "name": "checkout",
      "description": "Complete purchase",
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
            "name": { "type": "string", "required": true },
            "street": { "type": "string", "required": true },
            "city": { "type": "string", "required": true },
            "state": { "type": "string", "required": true },
            "zip": { "type": "string", "required": true },
            "country": { "type": "string", "required": true, "enum": ["US", "CA"] }
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
          "description": "Saved payment method or token"
        }
      },
      "output": {
        "type": "application/json",
        "schema": {
          "success": "boolean",
          "orderId": "string",
          "total": "number",
          "estimatedDelivery": "ISO date"
        }
      },
      "rateLimit": {
        "requests": 10,
        "window": "1m"
      }
    }
  ]
}
```

---

## Agent Execution

### Building Requests

```python
def execute_action(site, action_name, params):
    # 1. Fetch actions catalog
    catalog = fetch(f"{site}/actions/index.json")

    # 2. Find action
    action = next(a for a in catalog["actions"] if a["name"] == action_name)

    # 3. Build URL
    url = catalog["baseUrl"] + action["endpoint"]
    for key, value in params.items():
        url = url.replace(f"{{{key}}}", str(value))

    # 4. Build request
    if action["method"] == "GET":
        response = http_get(url, params=params)
    else:
        response = http_post(url, json=params)

    return response
```

### Error Handling

Agents SHOULD handle:
- `400` - Bad request (invalid parameters)
- `401` - Authentication required
- `403` - Not authorized
- `404` - Resource not found
- `429` - Rate limited
- `500` - Server error

---

*Actions transform reading into doing.*
