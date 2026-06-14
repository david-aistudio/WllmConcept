# Example: New Codebase Onboarding
<!-- example: onboarding -->

## Scenario
Agent encounters a new codebase for the first time.

---

## Phase 1: Structural Scan (Automatic)

```
Agent: Scanning codebase structure...

ENTRY POINTS IDENTIFIED:
  ├── src/main.ts (application bootstrap)
  ├── src/api/server.ts (HTTP server)
  └── src/workers/ (background jobs)

LANGUAGES: TypeScript (95%), SQL (3%), YAML (2%)
FRAMEWORK: Express.js + TypeORM
DATABASE: PostgreSQL

MODULE STRUCTURE:
  api/
  ├── routes/     (8 route files)
  ├── middleware/  (5 middleware files)
  ├── services/   (6 service files)
  └── utils/      (4 utility files)
  db/
  ├── migrations/ (12 migrations)
  └── queries/    (8 query files)
  workers/
  └── jobs/       (3 job files)

DEPENDENCY GRAPH:
  routes → middleware → services → db/queries
  routes → services → db/queries
  services → services (inter-service calls)
  workers → services → db/queries

COMPLEXITY HOTSPOTS:
  1. services/token.ts (complexity: 23)
  2. middleware/auth.ts (complexity: 18)
  3. services/api.ts (complexity: 15)

→ Created: wiki/structure/architecture.md
→ Created: wiki/structure/call-graph.md
→ Created: wiki/structure/dependencies.md
→ Created: wiki/structure/hotspots.md
```

## Phase 2: Semantic Extraction (Automatic)

```
Agent: Reading documentation and extracting knowledge...

FROM README.md:
  - This is an API management platform
  - Supports multiple auth strategies (JWT, API keys, OAuth)
  - Rate limiting per API key
  - Multi-tenant architecture

FROM docs/:
  - API documentation (OpenAPI spec found)
  - Deployment guide (Docker + Kubernetes)
  - Database schema documentation

FROM package.json:
  - 47 dependencies
  - Key: express, typeorm, jsonwebtoken, rate-limiter-flexible, ioredis

FROM comments:
  - "TODO: migrate to Fastify" (src/api/server.ts:15)
  - "HACK: workaround for TypeORM bug" (db/queries/user.ts:23)
  - "FIXME: race condition possible" (services/token.ts:45)

→ Created: wiki/pages/entities/api-management-platform.md
→ Created: wiki/pages/entities/jwt.md
→ Created: wiki/pages/entities/rate-limiter-flexible.md
→ Created: wiki/pages/concepts/multi-tenancy.md
→ Created: wiki/pages/concepts/auth-strategies.md
```

## Phase 3: Causal Inference (Semi-Automatic)

```
Agent: Analyzing git history for design decisions...

FROM GIT HISTORY:
  Commit abc123 (2026-01-15): "Switch from MongoDB to PostgreSQL"
    → Decision: PostgreSQL for ACID compliance
    → Created: wiki/causation/decisions/2026-01-15-postgres-over-mongo.md

  Commit def456 (2026-02-20): "Add mutex to token refresh"
    → Decision: Prevent race conditions in auth
    → Created: wiki/causation/decisions/2026-02-20-token-mutex.md

  Commit ghi789 (2026-03-10): "Replace token bucket with sliding window"
    → Decision: Better rate limiting behavior
    → Created: wiki/causation/decisions/2026-03-10-sliding-window-rate-limit.md

FROM PR DESCRIPTIONS:
  PR #45: "Fix rate limit headers being sent before middleware"
    → Bug fix, not a design decision
    → Updated: wiki/pages/concepts/rate-limiting.md (added gotcha)

ASKING USER:
  Agent: "I see a TODO comment about migrating to Fastify. Is this still planned?"
  User: "Yes, Q3 2026"
  → Created: wiki/causation/migrations/planned-fastify-migration.md
```

## Phase 4: Initial Wiki Build

```
Agent: Building initial wiki structure...

INDEX:
  wiki/index.md (38 pages)
  ├── Entities (8): api-platform, jwt, redis, postgresql, express, typeorm, rate-limiter, ioredis
  ├── Concepts (6): multi-tenancy, auth-strategies, rate-limiting, error-handling, caching, api-design
  ├── Structure (5): architecture, call-graph, dependencies, data-flow, hotspots
  ├── Decisions (3): postgres-over-mongo, token-mutex, sliding-window
  ├── Episodes (0): none yet
  └── Meta (0): none yet

OVERVIEW:
  wiki/overview.md
  "This is a TypeScript API management platform using Express.js,
  PostgreSQL, and Redis. It handles authentication via JWT and API keys,
  with per-key rate limiting. Multi-tenant architecture with plans to
  migrate to Fastify in Q3 2026."

GOTCHAS IDENTIFIED:
  wiki/pages/concepts/gotchas.md
  1. Rate limit headers are set BEFORE middleware runs (bug #45, fixed)
  2. Test environment uses in-memory store, not Redis
  3. TypeORM has a known bug with JSON columns (workaround in place)
  4. Token refresh has race condition protection (mutex)
```

## Phase 5: Monitoring Setup

```
Agent: Setting up ongoing monitoring...

FILE WATCHERS:
  ✓ Watching src/ for changes → trigger incremental ingest
  ✓ Watching docs/ for changes → trigger incremental ingest
  ✓ Watching package.json for changes → update dependency info

GIT HOOKS:
  ✓ post-commit → trigger incremental ingest
  ✓ post-merge → trigger full lint

PERIODIC TASKS:
  ✓ Daily lint → check wiki health
  ✓ Weekly self-reflection → update meta memory
  ✓ Monthly decision review → revisit causal memory
```

## Result

After onboarding, the agent has:

```
.codemind/
├── wiki/
│   ├── index.md (38 pages cataloged)
│   ├── overview.md (global summary)
│   ├── pages/ (14 entity/concept pages)
│   ├── structure/ (5 structural pages)
│   ├── causation/ (3 decision records)
│   ├── episodes/ (empty — will fill as agent works)
│   └── meta/ (empty — will fill as agent learns)
├── config.yaml
├── schema.md
└── purpose.md
```

**Time to onboard:** ~15 minutes (vs hours of manual documentation)
**Knowledge captured:** Architecture, decisions, gotchas, patterns
**Ready to:** Debug, implement features, answer questions — with full context
