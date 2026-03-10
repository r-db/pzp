# Security Specification

> Authentication, authorization, rate limiting, and data protection for PZP sites.

## Overview

While PZP sites are designed for open machine consumption, many require authentication for certain operations. This specification defines security patterns that balance accessibility with protection.

---

## Security Principles

### 1. Open by Default

Context and read operations SHOULD be publicly accessible. This enables:
- Agent discovery
- Content indexing
- Relevance determination

### 2. Protected by Necessity

Transactional operations SHOULD require authentication:
- User data access
- State-changing operations
- Resource creation/modification
- Purchases and transactions

### 3. Defense in Depth

Multiple security layers:
- Authentication (who are you?)
- Authorization (what can you do?)
- Rate limiting (how much can you do?)
- Validation (is your input safe?)

---

## Authentication

### Declaring Authentication

In `manifest.json`:

```json
{
  "authentication": {
    "required": false,
    "publicEndpoints": [
      "/context/",
      "/data/categories.json",
      "/api/products/",
      "/api/search"
    ],
    "protectedEndpoints": [
      "/api/cart/",
      "/api/checkout",
      "/api/orders/",
      "/api/user/"
    ],
    "methods": ["api-key", "oauth2", "session"]
  }
}
```

### Per-Action Authentication

In `actions/index.json`:

```json
{
  "name": "checkout",
  "authentication": {
    "required": true,
    "methods": ["api-key", "oauth2"]
  }
}
```

### Authentication Methods

#### API Key

Simple token-based authentication.

**Request:**
```http
GET /api/orders HTTP/1.1
Host: pzp.example.com
Authorization: Bearer pzp_live_abc123xyz
```

Or via header:
```http
X-API-Key: pzp_live_abc123xyz
```

**Manifest declaration:**
```json
{
  "methods": ["api-key"],
  "apiKey": {
    "header": "X-API-Key",
    "prefix": "pzp_"
  }
}
```

#### OAuth 2.0

Standard OAuth 2.0 flow for user-authorized access.

**Manifest declaration:**
```json
{
  "methods": ["oauth2"],
  "oauth2": {
    "authorizationUrl": "https://example.com/oauth/authorize",
    "tokenUrl": "https://example.com/oauth/token",
    "scopes": {
      "read": "Read user data",
      "write": "Modify user data",
      "cart": "Manage shopping cart",
      "checkout": "Complete purchases"
    }
  }
}
```

**Request with token:**
```http
GET /api/orders HTTP/1.1
Host: pzp.example.com
Authorization: Bearer eyJhbGciOiJSUzI1NiIs...
```

#### Session-Based

For agents maintaining stateful sessions.

**Manifest declaration:**
```json
{
  "methods": ["session"],
  "session": {
    "loginEndpoint": "/api/auth/login",
    "logoutEndpoint": "/api/auth/logout",
    "cookieName": "pzp_session"
  }
}
```

#### Basic Auth

HTTP Basic Authentication (not recommended for production).

**Request:**
```http
GET /api/data HTTP/1.1
Host: pzp.example.com
Authorization: Basic dXNlcjpwYXNz
```

---

## Authorization

### Capability-Based

Define what authenticated agents can do:

```json
{
  "authorization": {
    "capabilities": {
      "read": "Access public content",
      "search": "Search products",
      "cart": "Manage cart",
      "checkout": "Complete purchases",
      "orders": "View order history",
      "admin": "Administrative operations"
    },
    "defaultCapabilities": ["read", "search"],
    "authenticatedCapabilities": ["read", "search", "cart", "checkout", "orders"]
  }
}
```

### Resource-Based

Control access to specific resources:

```json
{
  "name": "get-order",
  "authorization": {
    "type": "owner",
    "description": "User can only access their own orders"
  }
}
```

### Scope-Based (OAuth)

Map OAuth scopes to capabilities:

```json
{
  "name": "checkout",
  "authentication": {
    "required": true,
    "oauth2Scopes": ["cart", "checkout"]
  }
}
```

---

## Rate Limiting

### Purpose

Protect against:
- Abuse and scraping
- Resource exhaustion
- Cost attacks

### Declaration

In `manifest.json`:

```json
{
  "rateLimits": {
    "default": {
      "requests": 100,
      "window": "1m"
    },
    "endpoints": {
      "/api/search": {
        "requests": 50,
        "window": "1m"
      },
      "/api/checkout": {
        "requests": 10,
        "window": "1m"
      }
    }
  }
}
```

### Response Headers

PZP sites SHOULD return rate limit headers:

```http
HTTP/1.1 200 OK
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1677721600
```

| Header | Description |
|--------|-------------|
| `X-RateLimit-Limit` | Maximum requests in window |
| `X-RateLimit-Remaining` | Remaining requests |
| `X-RateLimit-Reset` | Unix timestamp when window resets |

### Rate Limited Response

When rate limited:

```http
HTTP/1.1 429 Too Many Requests
Retry-After: 60
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1677721660

{
  "error": "rate_limited",
  "message": "Too many requests. Retry after 60 seconds.",
  "retryAfter": 60
}
```

### Window Formats

| Format | Duration |
|--------|----------|
| `1s` | 1 second |
| `1m` | 1 minute |
| `1h` | 1 hour |
| `1d` | 1 day |

---

## CORS

### Requirements

PZP sites MUST allow cross-origin requests for agent access:

```http
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, POST, PUT, DELETE, OPTIONS
Access-Control-Allow-Headers: Content-Type, Authorization, X-API-Key
Access-Control-Max-Age: 86400
```

### Preflight Handling

Sites MUST handle OPTIONS requests:

```http
OPTIONS /api/products HTTP/1.1
Host: pzp.example.com
Origin: https://agent.example.com
Access-Control-Request-Method: POST
Access-Control-Request-Headers: Content-Type, Authorization

HTTP/1.1 204 No Content
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, POST, PUT, DELETE, OPTIONS
Access-Control-Allow-Headers: Content-Type, Authorization, X-API-Key
```

---

## HTTPS

### Requirements

- PZP sites MUST serve over HTTPS
- HTTP requests SHOULD redirect to HTTPS
- HSTS headers SHOULD be set

```http
Strict-Transport-Security: max-age=31536000; includeSubDomains
```

### Certificate Requirements

- Valid certificate from trusted CA
- Not expired
- Covers the PZP domain

---

## Input Validation

### General Rules

All input MUST be validated:
- Type checking
- Range validation
- Format validation
- Length limits

### SQL Injection Prevention

- Use parameterized queries
- Never concatenate user input into queries
- Validate against expected patterns

### XSS Prevention

- Sanitize all output
- Use appropriate content types
- Avoid returning raw user input

### Path Traversal Prevention

- Validate file paths
- Reject `..` sequences
- Use allowlists for valid paths

---

## Data Protection

### Sensitive Data

Never expose in public endpoints:
- Passwords or credentials
- Personal identification numbers
- Full credit card numbers
- Private keys or tokens

### Data Minimization

Return only necessary data:

**Bad:**
```json
{
  "user": {
    "id": "123",
    "name": "John",
    "email": "john@example.com",
    "password_hash": "...",
    "ssn": "123-45-6789",
    "credit_cards": [...]
  }
}
```

**Good:**
```json
{
  "user": {
    "id": "123",
    "name": "John",
    "email": "j***@example.com"
  }
}
```

### Encryption

- Encrypt sensitive data at rest
- Use TLS for data in transit
- Rotate encryption keys regularly

---

## Error Handling

### Security-Aware Errors

Don't leak implementation details:

**Bad:**
```json
{
  "error": "SQLException: Table 'users' column 'password' not found",
  "stack": "..."
}
```

**Good:**
```json
{
  "error": "internal_error",
  "message": "An error occurred processing your request"
}
```

### Authentication Errors

```json
{
  "error": "unauthorized",
  "message": "Valid authentication required",
  "code": 401
}
```

### Authorization Errors

```json
{
  "error": "forbidden",
  "message": "You do not have permission for this action",
  "code": 403
}
```

---

## Security Headers

### Required Headers

```http
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
```

### Recommended Headers

```http
Content-Security-Policy: default-src 'self'
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: geolocation=(), microphone=()
```

---

## Security Checklist

### Implementation

- [ ] HTTPS enabled with valid certificate
- [ ] HSTS header configured
- [ ] CORS properly configured
- [ ] Rate limiting implemented
- [ ] Input validation on all endpoints
- [ ] Authentication for protected endpoints
- [ ] Authorization checks implemented
- [ ] Security headers set

### Data Protection

- [ ] No sensitive data in public endpoints
- [ ] Data minimization applied
- [ ] Encryption at rest for sensitive data
- [ ] Audit logging enabled
- [ ] PII handling compliant

### Error Handling

- [ ] Generic error messages (no leaks)
- [ ] Proper HTTP status codes
- [ ] Consistent error format

---

## Agent Behavior

### Handling Authentication

```python
def authenticated_request(site, endpoint, credentials):
    manifest = fetch(f"{site}/manifest.json")
    auth_config = manifest.get("authentication", {})

    headers = {}

    if "api-key" in auth_config.get("methods", []):
        key_header = auth_config.get("apiKey", {}).get("header", "X-API-Key")
        headers[key_header] = credentials.get("api_key")

    elif "oauth2" in auth_config.get("methods", []):
        token = get_oauth_token(auth_config["oauth2"], credentials)
        headers["Authorization"] = f"Bearer {token}"

    return fetch(f"{site}{endpoint}", headers=headers)
```

### Handling Rate Limits

```python
def rate_limited_request(url, max_retries=3):
    for attempt in range(max_retries):
        response = fetch(url)

        if response.status != 429:
            return response

        retry_after = int(response.headers.get("Retry-After", 60))
        sleep(retry_after)

    raise RateLimitExceeded()
```

---

*Security enables trust. Trust enables adoption.*
