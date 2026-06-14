# Workflow: Evolve
<!-- workflow: evolve -->

## Purpose
The agent learns from its experiences and improves itself. This is what makes CodeMind a BRAIN, not just a NOTEBOOK.

## When It Runs
- After every significant coding session
- After debugging sessions (when bug is resolved or abandoned)
- Periodically for self-reflection (default: weekly)
- After major code changes

## Pipeline

```
Experience → [Extract Lessons] → [Create Episode] → [Update Memories] → [Propagate] → [Self-Reflect]
```

### Step 1: Extract Lessons

After a session ends, analyze what happened:

```
LLM reviews the session and extracts:
├── What was the problem/task?
├── What approaches were tried?
├── Which approaches worked? Which failed?
├── What was the root cause (for bugs)?
├── What was the solution?
├── What patterns can be generalized?
├── What would I do differently next time?
└── What new knowledge was discovered?
```

**Prompt template:**
```
Review this coding session and extract lessons.

SESSION LOG:
{conversation log or summary}

TASK: {what the user asked for}
OUTCOME: {resolved / unresolved / abandoned}

Extract:
1. SYMPTOM: What was the observable problem?
2. ATTEMPTS: What did I try? (mark success/failure)
3. ROOT CAUSE: What was the actual cause?
4. SOLUTION: What fixed it?
5. LESSONS: Generalizable patterns for future use
6. HEURISTICS: "If I see X, try Y first next time"
7. NEW KNOWLEDGE: What did I learn about the codebase?
8. REGRET: What would I do differently?
```

### Step 2: Create Episode

If the session is significant enough, create an episode page:

**Significance criteria:**
- Debugging session lasted > 10 minutes
- Multiple approaches were tried
- A new pattern was discovered
- A failure occurred (failures are very valuable!)
- User explicitly says "remember this"

**Episode file:** `wiki/episodes/{category}/{date}-{slug}.md`

### Step 3: Update Memories

Based on lessons extracted, update ALL relevant memories:

| Memory Type | What to Update |
|------------|----------------|
| **Episodic** | Create new episode page |
| **Semantic** | Update entity/concept pages with new knowledge |
| **Structural** | Update call graphs, dependency info if code changed |
| **Causal** | Create/update decision records if a decision was made |
| **Meta** | Update heuristics, learning patterns, self-assessment |

### Step 4: Propagate Changes

When a page is updated, check dependent pages:

```
updated_pages = [list of pages modified in step 3]

for page in updated_pages:
  # Find pages that reference this page
  backlinks = find_pages_linking_to(page)
  
  for backlink in backlinks:
    # Check if the backlink page needs updating
    if backlink.references_stale_info(page):
      queue_for_update(backlink)
    
    # Propagate up to max_depth
    if depth < max_propagation_depth:
      propagate(backlink, depth + 1)
```

### Step 5: Self-Reflect

Periodically (default: weekly), the agent reflects on its own performance:

**What to reflect on:**

| Dimension | Question |
|-----------|----------|
| Accuracy | "How often was my first diagnosis correct?" |
| Efficiency | "How much time did I waste on wrong paths?" |
| Coverage | "What areas of the codebase do I understand well/poorly?" |
| Patterns | "What types of problems keep recurring?" |
| Growth | "What new skills/heuristics did I learn?" |
| Gaps | "What should I learn more about?" |

**Output:** Updated `wiki/meta/performance-review.md`

```markdown
---
type: meta
subtype: performance-review
period: 2026-06-07 to 2026-06-14
---

# Performance Review

## Sessions This Week
- 8 coding sessions
- 3 debugging sessions
- 2 feature implementations
- 1 code review

## Accuracy
- First diagnosis correct: 5/8 (62%)
- Needed 2+ attempts: 3/8 (38%)
- Improvement from last week: +12%

## Efficiency
- Time wasted on wrong paths: ~45min total
- Most common wrong path: checking code before checking config
- Average time to resolution: 18min (down from 25min)

## New Heuristics Learned
1. "Module not found" after npm install → check if package.json has `type: "module"`
2. TypeScript type errors after update → check @types/* versions first

## Areas to Improve
1. CSS layout debugging — 3 wrong diagnoses this week
2. Redis pub/sub patterns — need to study more

## Action Items
- [ ] Update CSS debugging heuristics
- [ ] Read Redis pub/sub documentation
- [ ] Create wiki page for module resolution patterns
```

---

## Episode Lifecycle

```
Session occurs
     │
     ▼
Significant? ──No──→ Log in log.md only
     │
    Yes
     │
     ▼
Create episode page
     │
     ▼
Extract lessons
     │
     ├──→ Update semantic memory (new facts)
     ├──→ Update structural memory (code changes)
     ├──→ Update causal memory (decisions)
     └──→ Update meta memory (heuristics)
              │
              ▼
        Propagate changes
              │
              ▼
        Periodic self-reflection
              │
              ▼
        Updated performance-review.md
```

---

## Failure Episodes (Most Valuable!)

Failures are MORE valuable than successes because they prevent repeating mistakes.

**Always create an episode for:**
- Wrong diagnosis that wasted time
- Approach that was abandoned
- Solution that caused side effects
- Assumption that turned out to be wrong

```markdown
---
type: episode
category: failure
date: 2026-06-14
duration: 30min
outcome: ABANDONED
difficulty: MEDIUM
---

# Failure: Wrong Auth Diagnosis

## What Happened
User reported 401 errors. I assumed JWT expiry issue.

## What I Did Wrong
1. ❌ Spent 15min checking JWT validation logic
2. ❌ Spent 10min checking token storage
3. ❌ Was about to suggest increasing token lifetime

## What Actually Fixed It
Race condition in token refresh (found by user, not me)

## Why I Was Wrong
- I pattern-matched on "401" → "token issue"
- I didn't check for intermittent patterns first
- I ignored the "random" part of the symptom

## Lesson
- [HEURISTIC] "Intermittent 401" ≠ "token expired"
- [HEURISTIC] "Random" symptoms → check concurrency first
- [PROCESS] Always ask: "Is it consistent or intermittent?" before diagnosing
```
