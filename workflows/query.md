# Workflow: Query
<!-- workflow: query -->

## Purpose
Answer questions by searching across all 5 memory systems and synthesizing answers with confidence levels.

## When It Runs
- User asks a question
- Agent needs context for a task
- Agent encounters unfamiliar code

## Pipeline

```
Question → [Classify Intent] → [Search Memories] → [Rank Results] → [Synthesize] → [File Answer]
```

### Step 1: Classify Intent

Determine what type of question this is:

| Intent | Primary Memory | Example Question |
|--------|---------------|-----------------|
| `what` | Semantic | "What does the auth service do?" |
| `how` | Structural | "How does the request flow work?" |
| `why` | Causal | "Why did we choose PostgreSQL?" |
| `when` | Episodic | "When did we fix the race condition?" |
| `history` | Episodic | "What debugging attempts did we try?" |
| `compare` | Semantic | "JWT vs session cookies — what's better for us?" |
| `pattern` | Meta | "What's my approach to debugging auth issues?" |

### Step 2: Search Memories

Search ALL 5 memory types, weighted by intent:

```
Search priority by intent:
  what   → Semantic (weight: 5), Structural (3), Causal (2), Episodic (1), Meta (1)
  how    → Structural (5), Semantic (3), Causal (2), Episodic (1), Meta (1)
  why    → Causal (5), Semantic (3), Episodic (3), Structural (1), Meta (1)
  when   → Episodic (5), Semantic (2), Causal (2), Structural (1), Meta (1)
  history → Episodic (5), Causal (3), Semantic (2), Structural (1), Meta (1)
  pattern → Meta (5), Episodic (3), Semantic (2), Structural (1), Causal (1)
```

**Search strategies:**
1. **Keyword match** — search page titles and content
2. **[[wikilink]] traversal** — follow links from relevant pages
3. **Index lookup** — read `index.md` to find relevant pages
4. **Graph walk** — follow knowledge graph edges
5. **Vector search** — semantic similarity (if vector enabled)

### Step 3: Rank Results

Rank by: `relevance × confidence × freshness`

```
score = relevance_weight × confidence_weight × freshness_weight

confidence_weight:
  VERIFIED = 1.0
  INFERRED = 0.8
  ASSUMED = 0.5
  CONTRADICTED = 0.2

freshness_weight:
  LOW staleness = 1.0
  MEDIUM = 0.9
  HIGH = 0.7
  STALE = 0.5
```

### Step 4: Synthesize Answer

**Prompt template:**
```
You are answering a question using a knowledge wiki.

QUESTION: {question}
INTENT: {intent}

RELEVANT PAGES (ranked by relevance):
---
{page 1 with confidence and freshness}
---
{page 2 with confidence and freshness}
---
...

RELEVANT EPISODES:
---
{episode 1}
---

RELEVANT CAUSAL KNOWLEDGE:
---
{decision 1}
---

RULES:
1. Cite sources using [[wikilinks]]
2. Mark confidence level for each claim:
   - [VERIFIED] — state as fact
   - [INFERRED] — "Based on the code..."
   - [ASSUMED] — "I believe... (not confirmed)"
3. If information conflicts, present both sides and note the conflict
4. If information is stale, note it
5. If the answer would make a good wiki page, say so

Answer the question.
```

### Step 5: File Answer (If Valuable)

If the answer is valuable and reusable, create a new wiki page:

**When to file:**
- The answer synthesizes information from 3+ pages
- The answer required significant reasoning
- The answer reveals a new connection or pattern
- The user explicitly says "save this" or "add to wiki"

**When NOT to file:**
- The answer is trivially available from one page
- The answer is time-sensitive (will be stale soon)
- The answer is specific to the current debugging context (use episode instead)

---

## Confidence in Answers

Every answer should include confidence indicators:

```markdown
The auth system uses JWT tokens [VERIFIED] with a 15-minute expiry [INFERRED].
The refresh mechanism uses a sliding window [VERIFIED] with mutex protection [VERIFIED].

Note: I believe the rate limit is 100 requests/minute [ASSUMED — check config to confirm].
```

---

## Multi-Memory Example

**Question:** "Why do we get intermittent 401 errors?"

**Search results across memories:**

| Memory | Finding | Confidence |
|--------|---------|------------|
| Episodic | "Debug session 2026-06-10: race condition in token refresh" | VERIFIED |
| Structural | "Auth flow: route → middleware → token service → JWT" | VERIFIED |
| Causal | "Token refresh uses mutex because of previous race condition" | VERIFIED |
| Semantic | "async-mutex library is installed for this purpose" | VERIFIED |
| Meta | "My heuristic: intermittent auth = check concurrency first" | INFERRED |

**Synthesized answer:**
"I've seen this exact pattern before. On 2026-06-10, we diagnosed a race condition in token refresh [[2026-06-10-auth-race-condition]]. The root cause was concurrent requests both reading the stale token. We fixed it with a mutex lock [[token-refresh-mutex]]. If you're seeing this again, check if the mutex is working correctly in the affected endpoint."
