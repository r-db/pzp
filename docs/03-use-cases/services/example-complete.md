# Complete SaaS PZP Implementation Example

> A fully working PZP site for "TaskFlow" - a project management SaaS platform.

## Overview

This is a complete, copy-paste-ready PZP implementation for a SaaS product. TaskFlow is a fictional project management platform that demonstrates every aspect of service-based PZP.

---

## File Structure

```
pzp.taskflow.io/
├── index.md                    # Entry point
├── manifest.json               # Site manifest
├── robots.txt                  # Discovery
├── sitemap.txt                 # File listing
│
├── context/
│   ├── essential.md            # Core context (~400 tokens)
│   ├── full.md                 # Complete context (~2000 tokens)
│   └── topics/
│       ├── pricing.md          # Pricing details
│       ├── integrations.md     # Available integrations
│       ├── security.md         # Security & compliance
│       └── api.md              # API overview
│
├── actions/
│   └── index.json              # All available actions
│
├── data/
│   ├── index.json              # Data directory
│   ├── features.json           # Feature catalog
│   ├── plans.json              # Pricing plans
│   ├── integrations.json       # Integration list
│   └── changelog.json          # Recent updates
│
└── meta/
    ├── version.json            # Version info
    └── status.json             # Service status
```

---

## Core Files

### index.md

```markdown
# TaskFlow PZP Interface

> Machine-native access to TaskFlow project management platform.

## Identity

- **Name:** TaskFlow
- **Type:** SaaS / Project Management
- **Version:** 1.0.0
- **Updated:** 2026-03-17

## What This Is

TaskFlow is a project management platform for modern teams. This PZP interface enables AI agents to understand our capabilities, check pricing, request demos, and integrate with user workflows.

## Quick Start

1. Read context: [/context/essential.md](/context/essential.md)
2. See actions: [/actions/index.json](/actions/index.json)
3. Get pricing: [/data/plans.json](/data/plans.json)

## Navigation

| Resource | Path | Description |
|----------|------|-------------|
| Essential Context | [/context/essential.md](/context/essential.md) | Core capabilities (~400 tokens) |
| Full Context | [/context/full.md](/context/full.md) | Complete documentation |
| Actions | [/actions/index.json](/actions/index.json) | Available operations |
| Features | [/data/features.json](/data/features.json) | Feature catalog |
| Pricing | [/data/plans.json](/data/plans.json) | Plan details |
| Integrations | [/data/integrations.json](/data/integrations.json) | Available integrations |

## Capabilities

- **Read:** Feature information, pricing, documentation
- **Search:** Find specific features or integrations
- **Request:** Demo requests, trial signups
- **Check:** Service status, plan availability
```

---

### manifest.json

```json
{
  "$schema": "https://pzp.riscent.com/schemas/manifest.schema.json",
  "version": "1.0.0",
  "protocolVersion": "1.0.0",
  "name": "TaskFlow",
  "description": "Project management platform for modern teams. Plan, track, and deliver projects with visual workflows, real-time collaboration, and powerful integrations.",
  "type": "service",
  "updated": "2026-03-17T00:00:00Z",
  "contact": {
    "email": "support@taskflow.io",
    "sales": "sales@taskflow.io"
  },
  "tokenBudgets": {
    "essential": 400,
    "full": 2000,
    "topics": {
      "pricing": 300,
      "integrations": 400,
      "security": 350,
      "api": 500
    }
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
    "request"
  ],
  "authentication": {
    "required": false,
    "publicEndpoints": [
      "/context/",
      "/data/features.json",
      "/data/plans.json",
      "/data/integrations.json",
      "/actions/request-demo",
      "/actions/start-trial"
    ],
    "protectedEndpoints": [
      "/api/projects",
      "/api/tasks",
      "/api/users"
    ],
    "methods": ["api-key", "oauth2"]
  },
  "rateLimits": {
    "default": {
      "requests": 100,
      "window": "1m"
    },
    "search": {
      "requests": 30,
      "window": "1m"
    }
  }
}
```

---

### robots.txt

```
User-agent: *
Allow: /

X-PZP-Site: true
X-PZP-Manifest: /manifest.json
X-PZP-Version: 1.0.0

Sitemap: /sitemap.txt
```

---

### sitemap.txt

```
/index.md
/manifest.json
/context/essential.md
/context/full.md
/context/topics/pricing.md
/context/topics/integrations.md
/context/topics/security.md
/context/topics/api.md
/actions/index.json
/data/index.json
/data/features.json
/data/plans.json
/data/integrations.json
/data/changelog.json
/meta/version.json
/meta/status.json
```

---

## Context Files

### context/essential.md

```markdown
# TaskFlow Essential Context

## What This Is

TaskFlow is a project management platform for modern teams. We help teams plan, track, and deliver projects with visual workflows, real-time collaboration, and powerful integrations.

## Key Capabilities

| Feature | Description |
|---------|-------------|
| Project Planning | Gantt charts, dependencies, milestones, critical path |
| Task Management | Kanban boards, assignments, priorities, due dates |
| Collaboration | Real-time comments, @mentions, file sharing |
| Time Tracking | Built-in timers, timesheets, billable hours |
| Reporting | Dashboards, burndown charts, team analytics |
| Integrations | 100+ apps: Slack, GitHub, Jira, Google, Microsoft |

## Pricing Tiers

| Plan | Price | Users | Best For |
|------|-------|-------|----------|
| Free | $0 | 5 | Personal projects |
| Starter | $9.99/user/mo | 10 | Small teams |
| Professional | $19.99/user/mo | 50 | Growing teams |
| Enterprise | Custom | Unlimited | Large organizations |

## Primary Actions

| Action | Description | Auth Required |
|--------|-------------|---------------|
| get-features | List all features | No |
| get-pricing | Get plan details | No |
| search-features | Search by keyword | No |
| request-demo | Schedule a demo | No |
| start-trial | Begin 14-day trial | No |
| check-status | Service status | No |

## Quick Links

- Features: [/data/features.json](/data/features.json)
- Pricing: [/data/plans.json](/data/plans.json)
- Integrations: [/data/integrations.json](/data/integrations.json)
- Security: [/context/topics/security.md](/context/topics/security.md)
```

---

### context/full.md

```markdown
# TaskFlow Full Context

## Overview

TaskFlow is a comprehensive project management platform designed for modern teams. Founded in 2020, we serve over 50,000 teams worldwide, from startups to Fortune 500 companies.

Our platform combines visual project planning, agile task management, and powerful reporting into a unified workspace that teams actually enjoy using.

## Core Features

### Project Planning

Visual project planning with professional-grade tools:

- **Gantt Charts:** Interactive timelines with drag-and-drop scheduling
- **Dependencies:** Finish-to-start, start-to-start, finish-to-finish, start-to-finish
- **Milestones:** Key deliverables with automatic notifications
- **Critical Path:** Automatic calculation and visualization
- **Baselines:** Save and compare against original plans
- **Resource Management:** Allocation, capacity planning, workload balancing

### Task Management

Flexible task organization for any workflow:

- **Multiple Views:** Kanban, list, calendar, timeline, workload
- **Custom Fields:** Text, numbers, dates, dropdowns, formulas
- **Priorities:** Critical, high, medium, low with color coding
- **Assignments:** Single or multiple assignees per task
- **Due Dates:** With reminders and overdue alerts
- **Subtasks:** Unlimited nesting, checklist style
- **Templates:** Reusable task and project templates

### Collaboration

Real-time teamwork features:

- **Comments:** Threaded discussions on any item
- **@Mentions:** Notify specific team members
- **File Sharing:** Attach files directly or link from cloud storage
- **Activity Feed:** See all changes across projects
- **Notifications:** Email, mobile push, in-app alerts
- **Guest Access:** Share with clients without full accounts

### Time Tracking

Built-in time management:

- **Timers:** One-click start/stop on any task
- **Manual Entry:** Log time after the fact
- **Timesheets:** Weekly view for reporting
- **Billable Hours:** Mark time as billable with rates
- **Estimates:** Set time estimates, track against actuals
- **Reports:** Time by project, user, client, date range

### Reporting & Analytics

Insights for informed decisions:

- **Dashboards:** Customizable with widgets
- **Burndown Charts:** Track sprint progress
- **Velocity:** Team performance over time
- **Utilization:** Resource efficiency metrics
- **Custom Reports:** Build any report you need
- **Scheduled Reports:** Automatic email delivery

## Integrations

### Communication

- Slack: Bi-directional sync, create tasks from messages
- Microsoft Teams: Full integration with tabs and bots
- Email: Create tasks via email, get notifications

### Development

- GitHub: Link commits, PRs to tasks
- GitLab: Issue sync, merge request tracking
- Jira: Two-way sync for hybrid teams
- Bitbucket: Repository integration

### Storage

- Google Drive: Attach files, preview in-app
- Dropbox: File linking and embedding
- OneDrive: Microsoft ecosystem integration
- Box: Enterprise file management

### Productivity

- Google Calendar: Sync due dates and milestones
- Outlook Calendar: Microsoft calendar sync
- Zapier: 3,000+ app automations
- API: Build custom integrations

## Security & Compliance

### Data Protection

- **Encryption:** AES-256 at rest, TLS 1.3 in transit
- **Data Centers:** SOC 2 Type II certified facilities
- **Backups:** Continuous replication, 30-day retention
- **Disaster Recovery:** RPO < 1 hour, RTO < 4 hours

### Access Control

- **SSO:** SAML 2.0, OAuth 2.0, OpenID Connect
- **2FA:** TOTP, SMS, hardware keys (FIDO2)
- **Role-Based Access:** Custom roles and permissions
- **IP Allowlisting:** Restrict access by network
- **Session Management:** Forced logout, timeout policies

### Compliance

- SOC 2 Type II certified
- GDPR compliant (EU data residency available)
- CCPA compliant
- HIPAA compliant (Enterprise plan)
- ISO 27001 certified

## Pricing Details

### Free Plan

- Up to 5 users
- 3 projects
- Basic features
- 500MB storage
- Community support

### Starter Plan - $9.99/user/month

- Up to 10 users
- Unlimited projects
- All core features
- 10GB storage per user
- Email support
- Integrations (limited)

### Professional Plan - $19.99/user/month

- Up to 50 users
- Everything in Starter
- Advanced features (Gantt, time tracking)
- 50GB storage per user
- Priority support
- All integrations
- Custom fields
- Automation rules

### Enterprise Plan - Custom Pricing

- Unlimited users
- Everything in Professional
- SSO/SAML
- Advanced security
- Unlimited storage
- Dedicated support
- SLA guarantees
- Custom integrations
- On-premise option

## API Overview

TaskFlow provides a comprehensive REST API for developers:

- **Base URL:** `https://api.taskflow.io/v1`
- **Authentication:** API key or OAuth 2.0
- **Rate Limits:** 1,000 requests/minute (Enterprise: unlimited)
- **Formats:** JSON
- **SDKs:** JavaScript, Python, Ruby, Go, PHP

Full API documentation: [https://docs.taskflow.io/api](https://docs.taskflow.io/api)

## Support

| Channel | Availability | Plans |
|---------|--------------|-------|
| Documentation | 24/7 | All |
| Community Forum | 24/7 | All |
| Email Support | Business hours | Starter+ |
| Live Chat | Business hours | Professional+ |
| Phone Support | 24/7 | Enterprise |
| Dedicated CSM | Always | Enterprise |

## Company

- **Founded:** 2020
- **Headquarters:** San Francisco, CA
- **Employees:** 200+
- **Customers:** 50,000+ teams
- **Status:** [status.taskflow.io](https://status.taskflow.io)
```

---

### context/topics/pricing.md

```markdown
# TaskFlow Pricing

## Plans Overview

| Plan | Monthly | Annual | Users | Best For |
|------|---------|--------|-------|----------|
| Free | $0 | $0 | 5 | Personal use, trying TaskFlow |
| Starter | $9.99/user | $99.99/user | 10 | Small teams getting started |
| Professional | $19.99/user | $199.99/user | 50 | Growing teams, full features |
| Enterprise | Custom | Custom | Unlimited | Large organizations |

Annual billing saves ~17%.

## Feature Comparison

| Feature | Free | Starter | Pro | Enterprise |
|---------|------|---------|-----|------------|
| Projects | 3 | Unlimited | Unlimited | Unlimited |
| Tasks | Unlimited | Unlimited | Unlimited | Unlimited |
| Storage | 500MB | 10GB/user | 50GB/user | Unlimited |
| Gantt Charts | - | - | Yes | Yes |
| Time Tracking | - | - | Yes | Yes |
| Custom Fields | - | Limited | Unlimited | Unlimited |
| Automations | - | - | Yes | Yes |
| Integrations | 3 | 10 | All | All + Custom |
| SSO/SAML | - | - | - | Yes |
| API Access | - | Limited | Full | Full + Dedicated |
| Support | Community | Email | Priority | Dedicated |

## Volume Discounts

| Users | Discount |
|-------|----------|
| 50-99 | 10% |
| 100-249 | 15% |
| 250-499 | 20% |
| 500+ | Custom |

## Trial

- 14-day free trial on any paid plan
- No credit card required
- Full feature access
- Data preserved if you upgrade
```

---

### context/topics/security.md

```markdown
# TaskFlow Security

## Data Protection

| Measure | Implementation |
|---------|----------------|
| Encryption at rest | AES-256 |
| Encryption in transit | TLS 1.3 |
| Key management | AWS KMS |
| Data centers | AWS (SOC 2 Type II) |
| Backups | Continuous, 30-day retention |

## Access Control

- **SSO:** SAML 2.0, OAuth 2.0, OpenID Connect (Enterprise)
- **2FA:** TOTP apps, SMS, FIDO2 hardware keys
- **Role-based access:** Admin, Manager, Member, Guest
- **IP allowlisting:** Enterprise plan
- **Session controls:** Configurable timeout, forced logout

## Compliance

| Standard | Status |
|----------|--------|
| SOC 2 Type II | Certified |
| ISO 27001 | Certified |
| GDPR | Compliant |
| CCPA | Compliant |
| HIPAA | Enterprise (BAA available) |

## Security Practices

- Annual penetration testing
- Bug bounty program
- Employee background checks
- Security awareness training
- Incident response plan
- Regular security audits

## Contact

Security inquiries: security@taskflow.io
```

---

## Actions File

### actions/index.json

```json
{
  "$schema": "https://pzp.riscent.com/schemas/actions.schema.json",
  "version": "1.0.0",
  "baseUrl": "https://pzp.taskflow.io",
  "actions": [
    {
      "name": "get-features",
      "description": "Get list of all TaskFlow features with plan availability",
      "endpoint": "/data/features.json",
      "method": "GET",
      "authentication": { "required": false },
      "input": {},
      "output": {
        "type": "application/json",
        "schema": {
          "features": "array of feature objects",
          "total": "number of features"
        }
      },
      "cacheDuration": 3600
    },
    {
      "name": "get-pricing",
      "description": "Get detailed pricing information for all plans",
      "endpoint": "/data/plans.json",
      "method": "GET",
      "authentication": { "required": false },
      "input": {},
      "output": {
        "type": "application/json",
        "schema": {
          "plans": "array of plan objects with pricing"
        }
      },
      "cacheDuration": 3600
    },
    {
      "name": "search-features",
      "description": "Search features by keyword or capability",
      "endpoint": "/api/features/search",
      "method": "GET",
      "authentication": { "required": false },
      "input": {
        "query": {
          "type": "string",
          "required": true,
          "description": "Search term"
        },
        "category": {
          "type": "string",
          "enum": ["planning", "tasks", "collaboration", "reporting", "integrations"],
          "description": "Filter by category"
        },
        "plan": {
          "type": "string",
          "enum": ["free", "starter", "professional", "enterprise"],
          "description": "Filter by plan availability"
        }
      },
      "output": {
        "schema": {
          "results": "matching features",
          "total": "number of matches"
        }
      }
    },
    {
      "name": "get-integrations",
      "description": "Get list of available integrations",
      "endpoint": "/data/integrations.json",
      "method": "GET",
      "authentication": { "required": false },
      "input": {
        "category": {
          "type": "string",
          "enum": ["communication", "development", "storage", "productivity", "all"],
          "default": "all"
        }
      },
      "output": {
        "schema": {
          "integrations": "array of integration objects"
        }
      }
    },
    {
      "name": "request-demo",
      "description": "Request a product demonstration",
      "endpoint": "/api/demo-request",
      "method": "POST",
      "authentication": { "required": false },
      "input": {
        "name": {
          "type": "string",
          "required": true,
          "description": "Contact name"
        },
        "email": {
          "type": "string",
          "required": true,
          "description": "Business email"
        },
        "company": {
          "type": "string",
          "description": "Company name"
        },
        "teamSize": {
          "type": "string",
          "enum": ["1-10", "11-50", "51-200", "201-500", "500+"],
          "description": "Number of potential users"
        },
        "useCase": {
          "type": "string",
          "description": "Primary use case or needs"
        },
        "preferredTime": {
          "type": "string",
          "description": "Preferred demo time (timezone)"
        }
      },
      "output": {
        "schema": {
          "requestId": "confirmation ID",
          "nextSteps": "what happens next"
        }
      }
    },
    {
      "name": "start-trial",
      "description": "Start a 14-day free trial",
      "endpoint": "/api/trial",
      "method": "POST",
      "authentication": { "required": false },
      "input": {
        "email": {
          "type": "string",
          "required": true,
          "description": "Work email address"
        },
        "name": {
          "type": "string",
          "required": true,
          "description": "Full name"
        },
        "company": {
          "type": "string",
          "description": "Company name"
        },
        "plan": {
          "type": "string",
          "enum": ["starter", "professional"],
          "default": "professional",
          "description": "Plan to trial"
        }
      },
      "output": {
        "schema": {
          "trialId": "trial identifier",
          "expiresAt": "trial end date",
          "loginUrl": "URL to access trial"
        }
      }
    },
    {
      "name": "check-status",
      "description": "Check TaskFlow service status",
      "endpoint": "/meta/status.json",
      "method": "GET",
      "authentication": { "required": false },
      "input": {},
      "output": {
        "schema": {
          "status": "operational | degraded | outage",
          "components": "status of each component",
          "lastUpdated": "ISO timestamp"
        }
      }
    },
    {
      "name": "compare-plans",
      "description": "Get detailed plan comparison",
      "endpoint": "/api/plans/compare",
      "method": "GET",
      "authentication": { "required": false },
      "input": {
        "plans": {
          "type": "array",
          "description": "Plans to compare (default: all)"
        }
      },
      "output": {
        "schema": {
          "comparison": "feature-by-feature comparison matrix"
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
  "description": "TaskFlow data files index",
  "files": [
    {
      "path": "/data/features.json",
      "description": "Complete feature catalog",
      "tokens": 800
    },
    {
      "path": "/data/plans.json",
      "description": "Pricing plans with details",
      "tokens": 400
    },
    {
      "path": "/data/integrations.json",
      "description": "Available integrations",
      "tokens": 500
    },
    {
      "path": "/data/changelog.json",
      "description": "Recent updates and releases",
      "tokens": 300
    }
  ]
}
```

---

### data/features.json

```json
{
  "version": "1.0.0",
  "generated": "2026-03-17T00:00:00Z",
  "features": [
    {
      "id": "gantt-charts",
      "name": "Gantt Charts",
      "description": "Interactive visual project timelines with drag-and-drop scheduling",
      "category": "planning",
      "plans": ["professional", "enterprise"],
      "highlights": [
        "Drag-and-drop scheduling",
        "Dependency visualization",
        "Critical path highlighting",
        "Baseline comparison"
      ]
    },
    {
      "id": "dependencies",
      "name": "Task Dependencies",
      "description": "Link tasks with finish-to-start, start-to-start, and other dependency types",
      "category": "planning",
      "plans": ["professional", "enterprise"],
      "highlights": [
        "Four dependency types",
        "Lag and lead time",
        "Automatic date shifting"
      ]
    },
    {
      "id": "kanban-boards",
      "name": "Kanban Boards",
      "description": "Visual workflow boards with customizable columns and swimlanes",
      "category": "tasks",
      "plans": ["free", "starter", "professional", "enterprise"],
      "highlights": [
        "Unlimited columns",
        "WIP limits",
        "Swimlanes",
        "Card templates"
      ]
    },
    {
      "id": "time-tracking",
      "name": "Time Tracking",
      "description": "Built-in time tracking with timers, manual entry, and reporting",
      "category": "reporting",
      "plans": ["professional", "enterprise"],
      "highlights": [
        "One-click timers",
        "Timesheets",
        "Billable rates",
        "Time reports"
      ]
    },
    {
      "id": "custom-fields",
      "name": "Custom Fields",
      "description": "Add custom data fields to projects and tasks",
      "category": "tasks",
      "plans": ["starter", "professional", "enterprise"],
      "limits": {
        "starter": 5,
        "professional": "unlimited",
        "enterprise": "unlimited"
      },
      "fieldTypes": ["text", "number", "date", "dropdown", "checkbox", "formula"]
    },
    {
      "id": "automations",
      "name": "Workflow Automations",
      "description": "Automate repetitive tasks with rule-based triggers and actions",
      "category": "productivity",
      "plans": ["professional", "enterprise"],
      "highlights": [
        "50+ automation templates",
        "Custom rules",
        "Multi-step workflows"
      ]
    },
    {
      "id": "real-time-collaboration",
      "name": "Real-Time Collaboration",
      "description": "See changes instantly across all team members",
      "category": "collaboration",
      "plans": ["free", "starter", "professional", "enterprise"],
      "highlights": [
        "Live updates",
        "Presence indicators",
        "Simultaneous editing"
      ]
    },
    {
      "id": "reporting-dashboards",
      "name": "Reporting Dashboards",
      "description": "Customizable dashboards with widgets and charts",
      "category": "reporting",
      "plans": ["starter", "professional", "enterprise"],
      "highlights": [
        "Drag-and-drop widgets",
        "Burndown charts",
        "Team velocity",
        "Custom reports"
      ]
    },
    {
      "id": "sso-saml",
      "name": "SSO/SAML",
      "description": "Single sign-on with enterprise identity providers",
      "category": "security",
      "plans": ["enterprise"],
      "providers": ["Okta", "Azure AD", "Google Workspace", "OneLogin", "Ping Identity"]
    },
    {
      "id": "api-access",
      "name": "API Access",
      "description": "REST API for custom integrations and automation",
      "category": "integrations",
      "plans": ["starter", "professional", "enterprise"],
      "limits": {
        "starter": "100 req/min",
        "professional": "1,000 req/min",
        "enterprise": "unlimited"
      }
    }
  ],
  "total": 10,
  "categories": ["planning", "tasks", "collaboration", "reporting", "integrations", "security", "productivity"]
}
```

---

### data/plans.json

```json
{
  "version": "1.0.0",
  "generated": "2026-03-17T00:00:00Z",
  "currency": "USD",
  "plans": [
    {
      "id": "free",
      "name": "Free",
      "description": "For personal projects and trying TaskFlow",
      "price": {
        "monthly": 0,
        "annual": 0
      },
      "limits": {
        "users": 5,
        "projects": 3,
        "storage": "500MB"
      },
      "features": [
        "kanban-boards",
        "real-time-collaboration",
        "mobile-apps",
        "basic-reporting"
      ],
      "support": "community"
    },
    {
      "id": "starter",
      "name": "Starter",
      "description": "For small teams getting started",
      "price": {
        "monthly": 9.99,
        "annual": 99.99,
        "perUser": true
      },
      "limits": {
        "users": 10,
        "projects": "unlimited",
        "storage": "10GB per user"
      },
      "features": [
        "everything-in-free",
        "custom-fields-limited",
        "reporting-dashboards",
        "integrations-limited",
        "api-access-limited"
      ],
      "support": "email"
    },
    {
      "id": "professional",
      "name": "Professional",
      "description": "For growing teams that need full features",
      "price": {
        "monthly": 19.99,
        "annual": 199.99,
        "perUser": true
      },
      "limits": {
        "users": 50,
        "projects": "unlimited",
        "storage": "50GB per user"
      },
      "features": [
        "everything-in-starter",
        "gantt-charts",
        "dependencies",
        "time-tracking",
        "custom-fields-unlimited",
        "automations",
        "all-integrations",
        "api-access-full"
      ],
      "support": "priority"
    },
    {
      "id": "enterprise",
      "name": "Enterprise",
      "description": "For large organizations with advanced needs",
      "price": {
        "type": "custom",
        "contact": "sales@taskflow.io"
      },
      "limits": {
        "users": "unlimited",
        "projects": "unlimited",
        "storage": "unlimited"
      },
      "features": [
        "everything-in-professional",
        "sso-saml",
        "advanced-security",
        "audit-logs",
        "custom-integrations",
        "api-unlimited",
        "on-premise-option",
        "dedicated-support"
      ],
      "support": "dedicated",
      "sla": {
        "uptime": "99.99%",
        "responseTime": "1 hour"
      }
    }
  ],
  "volumeDiscounts": [
    { "minUsers": 50, "discount": 10 },
    { "minUsers": 100, "discount": 15 },
    { "minUsers": 250, "discount": 20 },
    { "minUsers": 500, "discount": "custom" }
  ],
  "trial": {
    "duration": 14,
    "creditCardRequired": false,
    "plans": ["starter", "professional"]
  }
}
```

---

### data/integrations.json

```json
{
  "version": "1.0.0",
  "integrations": [
    {
      "id": "slack",
      "name": "Slack",
      "category": "communication",
      "description": "Create tasks from messages, get notifications in channels",
      "plans": ["starter", "professional", "enterprise"],
      "features": ["bi-directional-sync", "slash-commands", "notifications"]
    },
    {
      "id": "github",
      "name": "GitHub",
      "category": "development",
      "description": "Link commits and PRs to tasks, auto-update status",
      "plans": ["starter", "professional", "enterprise"],
      "features": ["commit-linking", "pr-tracking", "status-sync"]
    },
    {
      "id": "google-drive",
      "name": "Google Drive",
      "category": "storage",
      "description": "Attach files from Drive, preview documents in-app",
      "plans": ["starter", "professional", "enterprise"],
      "features": ["file-attachment", "preview", "search"]
    },
    {
      "id": "jira",
      "name": "Jira",
      "category": "development",
      "description": "Two-way sync for hybrid teams using both tools",
      "plans": ["professional", "enterprise"],
      "features": ["two-way-sync", "field-mapping", "real-time"]
    },
    {
      "id": "zapier",
      "name": "Zapier",
      "category": "productivity",
      "description": "Connect to 3,000+ apps with automated workflows",
      "plans": ["professional", "enterprise"],
      "features": ["triggers", "actions", "multi-step-zaps"]
    },
    {
      "id": "google-calendar",
      "name": "Google Calendar",
      "category": "productivity",
      "description": "Sync due dates and milestones to your calendar",
      "plans": ["starter", "professional", "enterprise"],
      "features": ["due-date-sync", "milestone-events", "two-way"]
    },
    {
      "id": "microsoft-teams",
      "name": "Microsoft Teams",
      "category": "communication",
      "description": "Full Teams integration with tabs, bots, and notifications",
      "plans": ["professional", "enterprise"],
      "features": ["tab-app", "bot", "notifications", "meetings"]
    },
    {
      "id": "gitlab",
      "name": "GitLab",
      "category": "development",
      "description": "Issue sync and merge request tracking",
      "plans": ["starter", "professional", "enterprise"],
      "features": ["issue-sync", "mr-tracking", "webhooks"]
    }
  ],
  "categories": [
    { "id": "communication", "name": "Communication", "count": 2 },
    { "id": "development", "name": "Development", "count": 3 },
    { "id": "storage", "name": "Storage", "count": 1 },
    { "id": "productivity", "name": "Productivity", "count": 2 }
  ],
  "total": 8
}
```

---

## Meta Files

### meta/version.json

```json
{
  "pzp": {
    "version": "1.0.0",
    "protocolVersion": "1.0.0",
    "updated": "2026-03-17T00:00:00Z"
  },
  "product": {
    "version": "4.2.0",
    "releaseDate": "2026-03-15",
    "changelog": "/data/changelog.json"
  }
}
```

---

### meta/status.json

```json
{
  "status": "operational",
  "lastUpdated": "2026-03-17T12:00:00Z",
  "components": [
    { "name": "Web Application", "status": "operational" },
    { "name": "API", "status": "operational" },
    { "name": "Real-time Sync", "status": "operational" },
    { "name": "File Storage", "status": "operational" },
    { "name": "Email Notifications", "status": "operational" }
  ],
  "incidents": [],
  "maintenance": [],
  "statusPage": "https://status.taskflow.io"
}
```

---

## Implementation Notes

### Deployment

This example can be deployed as:
1. **Static files** on any CDN (Vercel, Netlify, Cloudflare)
2. **Dynamic server** with real-time data from your product database

### Syncing with Product

For production use:
- Generate `features.json` from your feature flags system
- Generate `plans.json` from your billing system
- Generate `integrations.json` from your integration registry
- Update on deploy or via scheduled job

### API Endpoints

The example includes both static data files and dynamic API endpoints:
- Static: `/data/*.json` - Feature, pricing, integration data
- Dynamic: `/api/*` - Demo requests, trials, search

---

*TaskFlow: Project management that AI agents understand.*
