# Example: Debug Session with CodeMind
<!-- example: debug-session -->

## Scenario
User reports: "I'm getting 401 errors randomly on the dashboard"

---

## WITHOUT CodeMind (Current State)

```
Session 1 (Day 1):
  Agent: *reads entire codebase (5 min)*
  Agent: "Let me check the auth middleware..."
  Agent: "JWT validation looks correct."
  Agent: "Let me check the token refresh logic..."
  Agent: "I found it! Race condition in token refresh. Here's the fix."
  *Session ends. Agent forgets everything.*

Session 2 (2 months later):
  Agent: *reads entire codebase (5 min)*
  Agent: "Let me check the auth middleware..."
  Agent: "JWT validation looks correct."
  Agent: "Let me check..."
  *Same debugging path. Same 45 minutes wasted.*
```

**Total time wasted:** 45 min × 2 sessions = 90 minutes

---

## WITH CodeMind

### Session 1 (Day 1)

**Step 1: Agent checks episodic memory**
```
> Searching episodic memory for "401 error"...
> No exact match found.
> Related: "My debugging heuristic says: intermittent auth errors →
> check concurrency first"
```

**Step 2: Agent checks structural memory**
```
> Loading auth system structure...
> Auth flow:
>   request → rate-limit.ts → auth.ts → token.ts → jwt.ts
> Token refresh: token.ts → db/queries/token.sql → jwt.ts (sign)
> Hot path: called on every request
```

**Step 3: Agent checks causal memory**
```
> Loading design decisions...
> "Token refresh uses sliding window (not token bucket) because of
> burst issues. Decision from 2026-01."
```

**Step 4: Agent applies heuristic**
```
> Diagnosis based on heuristic + structural knowledge:
> - "Random 401" + "auth hot path" + "token refresh on every request"
> → Likely race condition in token refresh
> Confidence: INFERRED (not yet verified)
```

**Step 5: Agent investigates**
```
> Checking token.ts for concurrency issues...
> Found: Two concurrent requests can both read stale token
> Confirmed: Race condition exists
> Confidence promoted: INFERRED → VERIFIED
```

**Step 6: Agent fixes and EVOLVES**
```
> Fix: Added mutex lock on token refresh
> Time: 10 minutes (instead of 45)

> Creating episode: wiki/episodes/debug/2026-06-14-auth-race-condition.md
> Updating semantic memory: auth page now mentions mutex
> Updating structural memory: token.ts graph updated
> Creating causal memory: "Why we use mutex for token refresh"
> Updating meta memory: "Intermittent auth → concurrency check" heuristic confirmed
```

### Session 2 (2 months later)

```
User: "I'm getting 401 errors randomly on the admin panel"

Agent: *checks episodic memory*
> "AH! I've seen this exact pattern!
> Episode: 2026-06-14-auth-race-condition.md
> Root cause: Race condition in token refresh
> Fix: Mutex lock
> Let me check if admin panel has the same issue..."

Agent: *checks structural memory*
> "Admin panel uses same auth middleware as dashboard.
> Token refresh path: same token.ts service.
> But admin panel has a different middleware chain..."

Agent: "Found it! The admin panel's auth middleware was added AFTER
the mutex fix, but it has its own token refresh path without the mutex.
Same root cause, different location. Fix: apply mutex to admin path too."

Time: 5 minutes (instead of 45)
```

**Total time saved:** 80 minutes across 2 sessions

---

## What Happened Under the Hood

```
Session 1:
├── QUERY: Searched all 5 memories (episodic, structural, causal, semantic, meta)
├── INGEST: Read token.ts source code
├── EVOLVE: Created episode + updated 4 memory types
└── LINT: Checked related pages for consistency

Session 2:
├── QUERY: Found matching episode in episodic memory
├── QUERY: Used structural memory to find affected code
├── INGEST: Read admin panel middleware
└── EVOLVE: Created new episode linking to previous one
```

---

## Knowledge Growth

After these 2 sessions, the wiki now contains:

```
wiki/
├── pages/
│   ├── entities/
│   │   └── auth-service.md          [VERIFIED, updated]
│   └── concepts/
│       └── token-refresh.md         [VERIFIED, updated]
├── structure/
│   └── call-graph.md               [VERIFIED, updated with admin path]
├── causation/
│   └── decisions/
│       └── 2026-06-14-mutex-for-token-refresh.md  [VERIFIED, new]
├── episodes/
│   └── debug/
│       ├── 2026-06-14-auth-race-condition.md      [VERIFIED, new]
│       └── 2026-08-14-admin-panel-race.md         [VERIFIED, new]
└── meta/
    └── debugging-heuristics.md     [updated: new heuristic confirmed]
```

The agent is now SMARTER about auth issues. Every future session benefits.
