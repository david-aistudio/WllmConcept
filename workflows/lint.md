# Workflow: Lint
<!-- workflow: lint -->

## Purpose
Keep the wiki healthy, accurate, and synchronized with the codebase.

## When It Runs
- Periodically (default: every 24 hours)
- After major code changes
- Before a release
- On user request ("lint the wiki")

## Checks

### 1. Orphan Detection
**What:** Pages with no inbound links — nobody references them.
**Why:** Orphan pages are hard to discover and may be stale.

```
For each page in wiki/:
  inbound = count of other pages that [[wikilink]] to it
  if inbound == 0:
    flag as ORPHAN
    suggest: link from related pages or delete
```

### 2. Contradiction Detection
**What:** Pages that say conflicting things.
**Why:** Conflicting information erodes trust in the wiki.

```
For each pair of pages:
  extract key claims from both
  if claims conflict:
    flag as CONTRADICTION
    mark both pages as CONTRADICTED confidence
    add to human review queue
```

### 3. Staleness Detection
**What:** Pages that haven't been checked recently.
**Why:** Stale information can be worse than no information.

```
For each page:
  days_since_update = today - page.updated
  if days_since_update > freshness_window:
    flag as STALE
    check if source files have changed since last update
    if sources changed: queue for re-ingest
    if sources unchanged: just update freshness.last_checked
```

### 4. Code-Wiki Sync
**What:** Wiki pages that reference code that has changed.
**Why:** Code changes but wiki doesn't = stale documentation.

```
For each wiki page with sources:
  for each source file referenced:
    if file.hash != cache.hashes[file]:
      flag page as NEEDS UPDATE
      compare old content vs new content
      if significant change: queue for re-ingest
      if trivial change: just update freshness
```

### 5. Confidence Audit
**What:** Items with low confidence that could be verified.
**Why:** Low-confidence items reduce the wiki's usefulness.

```
For each page with confidence == ASSUMED:
  check if verification is now possible:
    - Can we read the code to verify?
    - Can we run a test to verify?
    - Can we ask the user to verify?
  if verification possible:
    add to verification queue
    suggest specific verification steps
```

### 6. Duplicate Detection
**What:** Pages that cover the same topic.
**Why:** Duplication causes confusion and maintenance burden.

```
For each pair of pages:
  similarity = content_similarity(page_a, page_b)
  if similarity > 0.8:
    flag as DUPLICATE
    suggest: merge into one page
```

### 7. Missing Page Detection
**What:** Concepts or entities mentioned in wiki but without their own page.
**Why:** Important knowledge gaps.

```
For each page:
  extract all [[wikilinks]]
  for each link:
    if target page doesn't exist:
      flag as MISSING PAGE
      suggest: create stub page
```

## Output

Lint produces an action items report:

```markdown
# Wiki Lint Report — 2026-06-14

## Summary
- Total pages: 47
- Health score: 82/100

## Action Items

### 🔴 Critical (fix now)
- CONTRADICTION: `rate-limiting.md` says token bucket, but code shows sliding window
- STALE: `auth.md` — code changed 3 days ago, wiki not updated

### 🟡 Important (fix this week)
- ORPHAN: `old-approach.md` — no pages link to it
- LOW CONFIDENCE: `redis-config.md` — ASSUMED, could verify by reading .env
- MISSING: `[[error-handling-pattern]]` referenced but page doesn't exist

### 🟢 Minor (fix when convenient)
- DUPLICATE: `api-routes.md` and `endpoint-list.md` cover same content
- FRESHNESS: 5 pages have MEDIUM staleness
```

## Auto-Fix vs Human Review

| Issue | Auto-Fix? | Action |
|-------|-----------|--------|
| Staleness (sources unchanged) | ✅ Yes | Update freshness timestamp |
| Staleness (sources changed) | ❌ No | Queue for re-ingest, notify human |
| Orphan (has related pages) | ✅ Yes | Add link from most related page |
| Orphan (no related pages) | ❌ No | Flag for human review |
| Missing page (mentioned 3+ times) | ✅ Yes | Create stub page |
| Missing page (mentioned 1-2 times) | ❌ No | Flag for human review |
| Contradiction | ❌ No | Always flag for human resolution |
| Low confidence (verifiable) | ❌ No | Add to verification queue |
| Duplicate | ❌ No | Flag for human merge decision |
