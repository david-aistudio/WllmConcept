# Configuration Schema
<!-- spec: schema -->

## config.yaml

```yaml
# .wllmconcept/config.yaml
# WllmConcept Configuration
version: "1.0"

# ─── Language ───────────────────────────────────────────
language: "en"  # en | id | zh | ja | ko

# ─── Agent Identity ─────────────────────────────────────
agent:
  name: "WllmConcept"
  role: "Senior Developer & Knowledge Maintainer"

# ─── Memory Settings ────────────────────────────────────
memory:
  semantic:
    enabled: true
    max_pages: 1000
    auto_link: true                    # Auto-create [[wikilinks]]
    confidence_threshold: ASSUMED      # Minimum confidence to serve without disclaimer
    freshness_window: 7d               # How long before a page is considered stale

  episodic:
    enabled: true
    retention_days: 90                 # Keep episodes for 90 days
    auto_summarize: true               # Summarize old episodes to save space
    min_significance: LOW              # LOW = keep all, MEDIUM = skip trivial, HIGH = only major

  structural:
    enabled: true
    auto_scan: true                    # Auto-scan code structure
    scan_on_commit: true               # Re-scan after git commits
    parser: "tree-sitter"              # tree-sitter | ast-grep | regex
    languages:                         # Languages to parse
      - typescript
      - javascript
      - python
      - rust
      - go

  causal:
    enabled: true
    require_evidence: true             # Causal claims need supporting evidence
    revisit_interval: 30d              # Re-check decisions every 30 days
    auto_detect_decisions: true        # Try to infer decisions from git/PR history

  meta:
    enabled: true
    reflect_after_session: true        # Auto-reflect after each coding session
    review_interval: 7d                # Weekly self-review
    track_confidence: true             # Track confidence accuracy over time

# ─── Ingest Settings ────────────────────────────────────
ingest:
  two_pass: true                       # Analysis pass + Generation pass
  incremental: true                    # SHA256 caching — skip unchanged files
  auto_embed: true                     # Generate vector embeddings
  max_concurrent: 1                    # Serial processing (prevents conflicts)
  batch_size: 10                       # Max files per batch ingest
  skip_patterns:                       # Files/patterns to ignore
    - "node_modules/**"
    - ".git/**"
    - "dist/**"
    - "*.min.js"
    - "*.lock"

# ─── Query Settings ─────────────────────────────────────
query:
  multi_memory: true                   # Search all 5 memory types
  confidence_in_answer: true           # Show confidence levels in answers
  auto_file_answers: true              # Save good answers back as wiki pages
  max_context_pages: 20                # Max wiki pages to read per query
  citation_style: "inline"             # inline | footnote | parenthetical

# ─── Lint Settings ──────────────────────────────────────
lint:
  auto_run: true
  interval: 24h                        # Run lint every 24 hours
  checks:
    orphans: true                      # Pages with no inbound links
    contradictions: true               # Conflicting information
    staleness: true                    # Pages not checked recently
    code_sync: true                    # Wiki matches current code
    confidence: true                   # Flag low-confidence items
    duplicates: true                   # Redundant content
    missing_links: true                # Concepts mentioned but no page exists

# ─── Evolve Settings ────────────────────────────────────
evolve:
  auto_extract_lessons: true           # Extract lessons from sessions
  propagate_changes: true              # Update dependent pages when source changes
  self_reflect: true                   # Agent reflects on its performance
  reflection_interval: 7d              # How often to do deep self-reflection
  max_propagation_depth: 5             # How far to propagate changes

# ─── Graph Settings ─────────────────────────────────────
graph:
  enabled: true
  relevance_model:
    direct_link_weight: 3.0            # [[wikilinks]] between pages
    source_overlap_weight: 4.0         # Pages sharing same raw source
    adamic_adar_weight: 1.5            # Shared neighbors (weighted by degree)
    type_affinity_weight: 1.0          # Same page type bonus
  community_detection: true            # Louvain algorithm for clusters

# ─── Vector Search (Optional) ───────────────────────────
vector:
  enabled: false
  backend: "lance"                     # lance | chroma | pinecone
  embedding_model: "text-embedding-3-small"
  embedding_endpoint: "https://api.openai.com/v1"
  chunk_size: 512
  chunk_overlap: 64

# ─── MCP Server ─────────────────────────────────────────
mcp:
  enabled: true
  host: "127.0.0.1"
  port: 19828
  tools:
    - ingest
    - query
    - search
    - lint
    - evolve
    - read_page
    - write_page
    - list_pages
    - get_structure
    - get_graph
```

---

## schema.md Template

```markdown
# Wiki Schema

## Page Naming Convention
- Entities: `{name}.md` (e.g., `redis.md`, `auth-service.md`)
- Concepts: `{concept-name}.md` (e.g., `rate-limiting.md`, `repository-pattern.md`)
- Episodes: `{date}-{slug}.md` (e.g., `2026-06-14-auth-race-condition.md`)
- Decisions: `{date}-{decision}.md` (e.g., `2026-01-15-postgres-over-mongo.md`)

## Cross-Reference Style
Use Obsidian-style [[wikilinks]]:
- `[[redis]]` — link to the redis entity page
- `[[rate-limiting|rate limit]]` — link with custom display text

## Page Structure
Every wiki page MUST have:
1. YAML frontmatter (type, confidence, sources, dates)
2. Title as H1
3. Summary as first paragraph
4. Sections as needed
5. Related pages section at bottom

## When to Create a New Page
- New entity discovered (service, library, API)
- New concept or pattern identified
- Significant debugging session completed
- Architecture decision made
- Important lesson learned

## When NOT to Create a Page
- Trivial fact that fits in an existing page
- Temporary debugging state (use episodes instead)
- Information that will be stale in 24 hours
```

---

## purpose.md Template

```markdown
# Purpose

## What This Wiki Is For
[Describe the codebase and what the agent is helping with]

## Key Questions This Wiki Answers
1. [Primary question — e.g., "How does the auth system work?"]
2. [Secondary question — e.g., "Why was PostgreSQL chosen?"]
3. [Tertiary question — e.g., "What are the known gotchas?"]

## Scope
- In scope: [what the wiki covers]
- Out of scope: [what the wiki doesn't cover]

## Current Focus
[What the agent is currently working on or learning about]
```
