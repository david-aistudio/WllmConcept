# The Five Memory Systems
<!-- concept: five-memories -->

A coding agent needs 5 types of memory, each serving a different purpose.

```
┌─────────────────────────────────────────────────────────────┐
│                      CODING AGENT BRAIN                      │
│                                                              │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐    │
│  │ SEMANTIC │  │ EPISODIC │  │STRUCTURAL│  │ CAUSAL   │    │
│  │ MEMORY   │  │ MEMORY   │  │ MEMORY   │  │ MEMORY   │    │
│  │          │  │          │  │          │  │          │    │
│  │ "What I  │  │ "What    │  │ "How code│  │ "Why     │    │
│  │  know"   │  │ happened │  │ is built"│  │ things   │    │
│  │          │  │ to me"   │  │          │  │ are the  │    │
│  │          │  │          │  │          │  │ way they │    │
│  │          │  │          │  │          │  │ are"     │    │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘    │
│       └──────────────┴──────┬──────┴──────────────┘          │
│                    ┌────────┴────────┐                       │
│                    │   META-MEMORY   │                       │
│                    │ "How I learn    │                       │
│                    │  and improve"   │                       │
│                    └─────────────────┘                       │
└─────────────────────────────────────────────────────────────┘
```

---

## Memory 1: Semantic Memory — "What I Know"

**Purpose:** Facts, concepts, relationships, patterns.

**Karpathy has this.** We enhance it with confidence scoring.

**Contains:**
- Entity pages (services, APIs, libraries)
- Concept pages (patterns, architectures, conventions)
- Source summaries (what each document/file says)
- Comparison pages (side-by-side analyses)

**Key Enhancement — Confidence Levels:**

| Level | Meaning | Example |
|-------|---------|---------|
| `VERIFIED` | Confirmed by code, tests, or user | "API returns JSON with `id` field" |
| `INFERRED` | Deduced from patterns, not confirmed | "Rate limit is probably 100/min" |
| `ASSUMED` | Educated guess | "Likely uses Redis for caching" |
| `CONTRADICTED` | Conflicts with other evidence | Needs human resolution |

**Key Enhancement — Freshness Tracking:**

Every page tracks when it was last verified:
```yaml
freshness:
  last_checked: 2026-06-14
  staleness: LOW  # LOW | MEDIUM | HIGH | STALE
```

**File location:** `wiki/pages/`

---

## Memory 2: Episodic Memory — "What Happened To Me"

**Purpose:** Remember past sessions, debugging attempts, conversations.

**This is NEW. Not in Karpathy.**

**Why it matters:**
Without episodic memory, the agent repeats the same debugging path every time. With it:
- "I've seen this pattern before — race condition. Check concurrency first."
- "Last time, approach X failed because of dependency Y."

**Contains:**

| Episode Type | What It Records | Value |
|-------------|-----------------|-------|
| `debug` | Debugging sessions — what was tried, what worked, what failed | Prevents repeating wrong paths |
| `decision` | Architectural decisions and their reasoning | Prevents re-debating settled questions |
| `refactor` | Code restructuring — what changed and why | Tracks code evolution |
| `incident` | Production issues — symptoms, root cause, fix | Institutional memory |
| `learning` | New patterns or techniques discovered | Accumulates wisdom |
| `failure` | Approaches that DIDN'T work (VERY valuable!) | Prevents repeating mistakes |

**Anatomy of an Episode:**

```markdown
---
type: episode
category: debug
date: 2026-06-10
duration: 45min
outcome: RESOLVED
difficulty: HARD
---

# Debug: Auth Race Condition

## Symptom
Intermittent 401 errors on /api/dashboard

## Attempts
1. ❌ Checked JWT expiry — valid (10min wasted)
2. ❌ Checked middleware order — correct (5min wasted)
3. ✅ Found race condition in token refresh (20min)

## Root Cause
Two concurrent requests → both read old token → both try to refresh

## Fix
Added mutex lock on token refresh (`async-mutex`)

## Lessons
- [LESSON] Intermittent auth errors → check concurrency FIRST
- [PATTERN] "Sometimes works, sometimes doesn't" → concurrency issue
```

**File location:** `wiki/episodes/`

---

## Memory 3: Structural Memory — "How Code Is Built"

**Purpose:** Understanding of code structure, not just content.

**This is NEW. Not in Karpathy.**

**Why it matters:**
Karpathy treats code as text. But code has STRUCTURE — functions call other functions, modules depend on other modules, data flows through pipelines. Understanding this structure is essential for a coding agent.

**Contains:**

| Page | What It Shows |
|------|--------------|
| `architecture.md` | High-level: monolith vs microservices, main entry points |
| `call-graph.md` | Who calls whom — function-level relationships |
| `dependencies.md` | Module dependency tree |
| `data-flow.md` | How data moves through the system |
| `hotspots.md` | Complexity hotspots — most complex code |
| `patterns.md` | Detected patterns — auth, error handling, caching |

**Example — Call Graph:**
```
Request Flow:
  api/routes/auth.ts
    → middleware/auth.ts
      → services/token.ts
        → libs/jwt.ts (verify)
        → db/queries/token.sql (lookup)
    → middleware/rate-limit.ts
      → libs/redis.ts (counter)
```

**How It's Built:**
- Auto-generated from AST parsing (tree-sitter)
- Updated when code changes (git hooks or file watchers)
- NOT just a file tree — shows RELATIONSHIPS and FLOW

**File location:** `wiki/structure/`

---

## Memory 4: Causal Memory — "Why Things Are The Way They Are"

**Purpose:** Capture design decisions, trade-offs, and reasoning.

**This is NEW. Not in Karpathy. This is the MOST VALUABLE layer.**

**Why it matters:**
When the agent sees code that looks "wrong" (e.g., using PostgreSQL for JSON data), it can check causal memory: "Oh, they chose PostgreSQL for ACID requirements. The JSONB usage makes sense." Without this, the agent might "fix" it and break the architecture.

**Contains:**

| Type | What It Records |
|------|----------------|
| `decision` | Architecture decisions with full reasoning |
| `tradeoff` | What was sacrificed and why |
| `migration` | Why something changed from old to new |

**Anatomy of a Decision Record:**

```markdown
---
type: decision
date: 2026-01-15
status: ACTIVE
revisit: 2026-07-15
---

# Decision: PostgreSQL over MongoDB

## Context
Need a primary database for e-commerce platform.

## Decision
Use PostgreSQL with JSONB for flexible fields.

## Why (Causal Chain)
1. Need ACID for orders → MongoDB transactions are weaker
2. Data is relational → MongoDB needs $lookup everywhere
3. Need full-text search → PostgreSQL has tsvector
4. Team knows SQL → lower onboarding cost
5. JSONB gives flexibility where needed → best of both

## Alternatives Rejected
- MongoDB: weak ACID, poor relational support
- MySQL: weaker JSON support
- CockroachDB: overkill for current scale

## Tradeoffs Accepted
- Harder horizontal scaling (don't need yet)
- More migration work (but more reliable)
```

**File location:** `wiki/causation/`

---

## Memory 5: Meta-Memory — "How I Learn and Improve"

**Purpose:** The agent reflects on its own performance and improves.

**This is NEW. Not in Karpathy. This is what makes the agent SELF-IMPROVING.**

**Why it matters:**
A coding agent that never reflects on its mistakes will keep making them. Meta-memory lets the agent:
- Track what it's good and bad at
- Learn debugging heuristics from experience
- Calibrate its confidence and time estimates
- Identify patterns in its own failures

**Contains:**

| Page | What It Tracks |
|------|---------------|
| `learning-patterns.md` | Strengths, weaknesses, common mistakes |
| `debugging-heuristics.md` | Rules learned from past debugging |
| `confidence-audit.md` | Low-confidence items that need verification |
| `performance-review.md` | Periodic self-assessment |

**Example — Debugging Heuristics:**

```markdown
# Debugging Heuristics (learned from experience)

## Symptom → Most Likely Cause
- "Sometimes works, sometimes doesn't" → concurrency issue
- "Works in dev, fails in prod" → environment/config difference
- "Works for small data, fails for large" → performance/resource issue
- "Works after restart, fails later" → memory leak or connection pool
- "Works on first request, fails on second" → caching or session issue

## Process Heuristics (learned from mistakes)
1. Always check config BEFORE code for env-specific bugs
2. Always check git blame — bugs are often in recently changed code
3. Run tests BEFORE suggesting changes
4. Start with simplest approach, iterate (don't over-engineer)
```

**File location:** `wiki/meta/`

---

## How The Five Memories Work Together

**Scenario: User reports "401 errors randomly"**

```
1. Check EPISODIC memory
   → "I've seen intermittent auth bugs before — race condition"

2. Check STRUCTURAL memory
   → "Auth flow: route → middleware → token service → JWT"

3. Check CAUSAL memory
   → "Token refresh uses sliding window because of burst issues"

4. Check SEMANTIC memory
   → "async-mutex is already installed for token refresh"

5. Check META memory
   → "My heuristic says: intermittent auth = concurrency first"

6. Result: Diagnose race condition in 10 minutes instead of 45

7. After fix: EVOLVE all memories
   → New episode, updated pages, new heuristic pattern
```
