# Workflow: Ingest
<!-- workflow: ingest -->

## Purpose
Bring new knowledge into the system. Transform raw sources into structured wiki pages.

## When It Runs
- User adds a new source document
- Code file changes (git hook or file watcher)
- User says "ingest this" or "add this to the wiki"
- Batch import (folder of documents)

## Pipeline

```
Source ──→ [Pass 1: Analyze] ──→ [Pass 2: Generate] ──→ [Link] ──→ [Verify] ──→ [Cache]
```

### Step 1: Analyze (First LLM Call)

**Input:** Raw source file
**Output:** Structured analysis

```
LLM reads the source and extracts:
├── Key entities (services, libraries, APIs, people)
├── Key concepts (patterns, techniques, ideas)
├── Key facts (configuration values, endpoints, behaviors)
├── Connections to existing wiki pages
├── Contradictions with existing knowledge
├── Questions that need answers
└── Recommended wiki structure
```

**Prompt template:**
```
You are analyzing a source document for a knowledge wiki.

SOURCE: {filename}
CONTENT: {content}

EXISTING WIKI INDEX:
{index.md content}

Analyze this source and extract:
1. KEY ENTITIES: services, libraries, APIs, people mentioned
2. KEY CONCEPTS: patterns, techniques, architectural ideas
3. KEY FACTS: specific claims, configurations, behaviors
4. CONNECTIONS: which existing wiki pages does this relate to?
5. CONTRADICTIONS: does anything here conflict with existing knowledge?
6. QUESTIONS: what's unclear or needs more investigation?
7. RECOMMENDATIONS: what wiki pages should be created or updated?

Be specific. Reference existing pages by [[wikilink]].
```

### Step 2: Generate (Second LLM Call)

**Input:** Analysis from Step 1 + existing wiki context
**Output:** Wiki page files to create/update

```
For each entity/concept identified:
├── Create new page OR update existing page
├── Add YAML frontmatter (type, confidence, sources)
├── Write content with [[wikilinks]] to related pages
├── Include gotchas and notes
└── Add to index.md

For each contradiction found:
├── Flag with CONTRADICTED confidence
├── Note both versions
└── Add to review queue for human
```

**Prompt template:**
```
Based on the analysis below, generate wiki pages.

ANALYSIS:
{analysis from step 1}

EXISTING PAGES TO UPDATE:
{relevant existing page contents}

RULES:
1. Every page MUST have YAML frontmatter with type, confidence, sources
2. Use [[wikilinks]] for cross-references
3. Confidence: VERIFIED if directly stated in source, INFERRED if deduced
4. Don't duplicate existing content — update and extend
5. Flag contradictions with CONTRADICTED confidence
6. Keep pages focused — one topic per page

Generate:
- New pages (full content)
- Updates to existing pages (diff format)
- index.md updates
- log.md entry
```

### Step 3: Link

After pages are generated:
1. Find all [[wikilinks]] in new/updated pages
2. Verify target pages exist (create stubs if not)
3. Add backlinks (if A links to B, add "referenced by A" to B)
4. Update graph data (nodes.json, edges.json)

### Step 4: Verify

Assign confidence levels:
- Claims directly from code → `VERIFIED`
- Claims from documentation → `INFERRED` (docs can be stale)
- Claims from comments → `ASSUMED` (comments can be wrong)
- Claims that conflict → `CONTRADICTED`

### Step 5: Cache

1. Calculate SHA256 of source file
2. Store in `cache/hashes.json`
3. Next ingest of same file: skip if hash unchanged

---

## Incremental Ingest

```
For each file in ingest queue:
  1. Calculate SHA256
  2. Compare with cache/hashes.json
  3. If unchanged → skip
  4. If changed → run full pipeline
  5. Propagate changes to dependent pages
```

## Structural Ingest (For Code Files)

After regular ingest, also:
1. Parse AST with tree-sitter
2. Extract function signatures, types, imports
3. Build call graph edges
4. Calculate complexity metrics
5. Update `wiki/structure/` pages

---

## Error Handling

| Error | Action |
|-------|--------|
| LLM timeout | Retry 3x with exponential backoff |
| Parse error | Log error, skip file, continue queue |
| Conflict detected | Flag for human review, continue |
| Queue crash | Resume from `cache/ingest-queue.json` |
