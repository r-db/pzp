# Services PZP Implementation

> How to implement PZP for SaaS platforms, booking systems, and professional services.

## Overview

Service businesses—SaaS platforms, appointment systems, consulting firms, agencies—benefit enormously from PZP. AI agents increasingly help users find, evaluate, and engage with services. Without PZP, agents struggle to understand your offering and capabilities.

With PZP, agents can:
- Understand your service offerings
- Check availability and pricing
- Book appointments or demos
- Integrate with their workflows

---

## Why Services Need PZP

### The Discovery Problem

When users ask AI "find me a project management tool with Gantt charts":
- Agent searches the web
- Agent finds your landing page (HTML, JavaScript)
- Agent parses marketing copy, guessing at features
- Agent may misunderstand or miss your offering entirely

### The Integration Opportunity

With PZP:
- Agent reads structured service capabilities
- Agent understands features, pricing, constraints
- Agent can check availability, book demos
- Agent accurately represents your service

---

## Services PZP Structure

```
pzp.yourservice.com/
├── index.md                    # Service entry point
├── manifest.json               # Service manifest
├── robots.txt                  # Discovery
│
├── context/
│   ├── essential.md            # Service overview (~400 tokens)
│   ├── full.md                 # Complete capabilities
│   └── topics/
│       ├── pricing.md          # Pricing details
│       ├── integrations.md     # Integration options
│       └── security.md         # Security & compliance
│
├── actions/
│   └── index.json              # Service actions
│
├── data/
│   ├── features.json           # Feature list
│   ├── plans.json              # Pricing plans
│   └── integrations.json       # Available integrations
│
└── meta/
    └── version.json
```

---

## Documents in This Section

| Document | Description |
|----------|-------------|
| [SaaS](saas.md) | SaaS platform implementation |
| [Booking](booking.md) | Appointment booking systems |
| [Consulting](consulting.md) | Professional services |
| [Local Business](local-business.md) | Local service businesses |
| [Complete Example](example-complete.md) | Full SaaS PZP site |

---

## Key Actions for Services

### Check Availability

```json
{
  "name": "check-availability",
  "description": "Check appointment availability",
  "endpoint": "/api/availability",
  "method": "GET",
  "input": {
    "service": { "type": "string", "required": true },
    "date": { "type": "string", "description": "ISO date" },
    "duration": { "type": "integer", "description": "Minutes" }
  },
  "output": {
    "type": "application/json",
    "schema": {
      "slots": "array of available time slots",
      "timezone": "timezone of slots"
    }
  }
}
```

### Book Appointment

```json
{
  "name": "book-appointment",
  "description": "Book a service appointment",
  "endpoint": "/api/appointments",
  "method": "POST",
  "authentication": { "required": true },
  "input": {
    "service": { "type": "string", "required": true },
    "datetime": { "type": "string", "required": true },
    "duration": { "type": "integer", "required": true },
    "notes": { "type": "string" }
  },
  "output": {
    "schema": {
      "confirmationId": "string",
      "datetime": "ISO datetime",
      "meetingLink": "URL if virtual"
    }
  }
}
```

### Get Pricing

```json
{
  "name": "get-pricing",
  "description": "Get pricing information",
  "endpoint": "/data/plans.json",
  "method": "GET",
  "input": {},
  "output": {
    "schema": {
      "plans": "array of pricing plans",
      "currency": "USD"
    }
  }
}
```

### Request Demo

```json
{
  "name": "request-demo",
  "description": "Request a product demo",
  "endpoint": "/api/demo-request",
  "method": "POST",
  "input": {
    "name": { "type": "string", "required": true },
    "email": { "type": "string", "required": true },
    "company": { "type": "string" },
    "useCase": { "type": "string" },
    "preferredTimes": { "type": "array" }
  }
}
```

---

## Service Data Schema

### Feature Definition

```json
{
  "features": [
    {
      "id": "gantt-charts",
      "name": "Gantt Charts",
      "description": "Visual project timelines with dependencies",
      "category": "project-management",
      "plans": ["pro", "enterprise"],
      "status": "available"
    },
    {
      "id": "team-collaboration",
      "name": "Team Collaboration",
      "description": "Real-time collaboration with comments and mentions",
      "category": "collaboration",
      "plans": ["all"],
      "status": "available"
    }
  ]
}
```

### Pricing Plan

```json
{
  "plans": [
    {
      "id": "starter",
      "name": "Starter",
      "description": "For individuals and small teams",
      "price": {
        "monthly": 9.99,
        "annual": 99.99,
        "currency": "USD"
      },
      "features": ["basic-features", "5-users", "5gb-storage"],
      "limits": {
        "users": 5,
        "storage": "5GB",
        "projects": 10
      }
    },
    {
      "id": "pro",
      "name": "Professional",
      "description": "For growing teams",
      "price": {
        "monthly": 29.99,
        "annual": 299.99,
        "currency": "USD"
      },
      "features": ["all-starter", "gantt-charts", "integrations"],
      "limits": {
        "users": 25,
        "storage": "50GB",
        "projects": "unlimited"
      }
    },
    {
      "id": "enterprise",
      "name": "Enterprise",
      "description": "For large organizations",
      "price": {
        "type": "custom",
        "contact": "sales@yourservice.com"
      },
      "features": ["all-pro", "sso", "audit-logs", "dedicated-support"],
      "limits": {
        "users": "unlimited",
        "storage": "unlimited",
        "projects": "unlimited"
      }
    }
  ]
}
```

### Availability Slot

```json
{
  "slots": [
    {
      "datetime": "2026-03-07T09:00:00-05:00",
      "duration": 30,
      "available": true,
      "service": "consultation"
    },
    {
      "datetime": "2026-03-07T09:30:00-05:00",
      "duration": 30,
      "available": true,
      "service": "consultation"
    },
    {
      "datetime": "2026-03-07T10:00:00-05:00",
      "duration": 30,
      "available": false,
      "service": "consultation"
    }
  ],
  "timezone": "America/New_York",
  "date": "2026-03-07"
}
```

---

## Context Examples

### Essential Context for SaaS

```markdown
# ProjectFlow Essential Context

## What This Is

ProjectFlow is a project management platform for modern teams. We help teams plan, track, and deliver projects with visual workflows, real-time collaboration, and powerful integrations.

## Key Capabilities

| Feature | Description |
|---------|-------------|
| Project Planning | Gantt charts, dependencies, milestones |
| Task Management | Kanban boards, assignments, priorities |
| Collaboration | Comments, mentions, file sharing |
| Reporting | Dashboards, time tracking, analytics |
| Integrations | Slack, GitHub, Jira, 100+ apps |

## Pricing Tiers

| Plan | Price | Best For |
|------|-------|----------|
| Starter | $9.99/mo | Individuals, small teams |
| Pro | $29.99/mo | Growing teams (25 users) |
| Enterprise | Custom | Large organizations |

## Primary Actions

| Action | Description |
|--------|-------------|
| get-features | List all features by plan |
| get-pricing | Get detailed pricing |
| request-demo | Schedule a demo |
| check-status | Check service status |
```

### Essential Context for Booking

```markdown
# Downtown Dental Essential Context

## What This Is

Downtown Dental is a modern dental practice offering comprehensive dental care. We provide preventive, cosmetic, and restorative dentistry with online booking and flexible scheduling.

## Services

| Service | Duration | Price |
|---------|----------|-------|
| Cleaning | 30 min | $120 |
| Exam | 45 min | $80 |
| Whitening | 60 min | $350 |
| Filling | 30 min | $150-300 |
| Crown | 90 min | $800-1200 |

## Booking Information

- **Hours:** Mon-Fri 8am-6pm, Sat 9am-2pm
- **Location:** 123 Main St, Downtown
- **Insurance:** Most major providers accepted
- **New Patients:** Welcome, same-week availability

## Primary Actions

| Action | Description |
|--------|-------------|
| check-availability | Find open appointment slots |
| book-appointment | Book a dental appointment |
| get-services | List services with pricing |
```

---

## Authentication Patterns

### Public vs Protected

| Action | Auth Required | Notes |
|--------|---------------|-------|
| Get features | No | Public information |
| Get pricing | No | Public information |
| Check availability | No | General availability |
| Book appointment | Yes | Requires account |
| Request demo | No | Creates lead |
| Access account | Yes | User-specific |

### API Key for Integrations

```json
{
  "authentication": {
    "required": false,
    "publicEndpoints": [
      "/data/",
      "/context/",
      "/api/availability"
    ],
    "protectedEndpoints": [
      "/api/appointments",
      "/api/account/"
    ],
    "methods": ["api-key", "oauth2"],
    "apiKey": {
      "header": "X-API-Key",
      "obtainUrl": "https://yourservice.com/settings/api"
    }
  }
}
```

---

## Integration with Calendars

### Exposing Calendar Data

```json
{
  "name": "get-calendar",
  "description": "Get calendar for date range",
  "endpoint": "/api/calendar",
  "method": "GET",
  "authentication": { "required": true },
  "input": {
    "start": { "type": "string", "description": "ISO date" },
    "end": { "type": "string", "description": "ISO date" }
  },
  "output": {
    "schema": {
      "events": "array of appointments",
      "availability": "available slots in range"
    }
  }
}
```

### Calendar Sync

```json
{
  "integrations": {
    "calendar": {
      "google": {
        "supported": true,
        "syncType": "two-way"
      },
      "outlook": {
        "supported": true,
        "syncType": "two-way"
      },
      "ical": {
        "supported": true,
        "feedUrl": "/api/calendar.ics"
      }
    }
  }
}
```

---

## Common Patterns

### Service Catalog

```json
{
  "services": [
    {
      "id": "consultation",
      "name": "Initial Consultation",
      "description": "30-minute consultation to discuss your needs",
      "duration": 30,
      "price": 0,
      "type": "virtual",
      "bookable": true
    },
    {
      "id": "implementation",
      "name": "Implementation Session",
      "description": "2-hour guided implementation",
      "duration": 120,
      "price": 500,
      "type": "virtual",
      "bookable": true,
      "requiresConsultation": true
    }
  ]
}
```

### Team/Provider Selection

```json
{
  "providers": [
    {
      "id": "dr-smith",
      "name": "Dr. Sarah Smith",
      "title": "Lead Dentist",
      "specialties": ["cosmetic", "restorative"],
      "availability": "/api/availability?provider=dr-smith"
    }
  ]
}
```

### Multi-Location

```json
{
  "locations": [
    {
      "id": "downtown",
      "name": "Downtown Office",
      "address": "123 Main St",
      "timezone": "America/New_York",
      "services": ["all"],
      "availability": "/api/availability?location=downtown"
    },
    {
      "id": "suburban",
      "name": "Suburban Office",
      "address": "456 Oak Ave",
      "timezone": "America/New_York",
      "services": ["cleaning", "exam"],
      "availability": "/api/availability?location=suburban"
    }
  ]
}
```

---

## Success Metrics

| Metric | Description |
|--------|-------------|
| Demo requests via PZP | Demos booked through agent access |
| Feature queries | How often features are queried |
| Booking completion | Appointments booked via PZP |
| Integration usage | API key creation and usage |

---

*Services that agents can book. Businesses that agents recommend.*
