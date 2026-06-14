# Architecture Specification
<!-- spec: architecture -->

## Directory Structure

```
.codemind/
├── config.yaml                 # System configuration
├── schema.md                   # Wiki conventions and rules
├── purpose.md                  # Why this wiki exists
│
├── wiki/                       # THE KNOWLEDGE BASE
│   ├── index.md                # Content catalog (navigation)
│   ├── log.md                  # Chronological operations log
│   ├── overview.md             # Global summary of the codebase
│   │
│   ├── pages/                  # [SEMANTIC] Facts, concepts, entities
│   │   ├── entities/           # Services, APIs, libraries, people
│   │   ├── concepts/           # Patterns, architectures, conventions
│   │   ├── sources/            # Summaries of source documents
│   │   └── comparisons/        # Side-by-side analyses
│   │
│   ├── structure/              # [STRUCTURAL] Code organization
│   │   ├── architecture.md     # High-level architecture
│   │   ├── call-graph.md       # Function call relationships
│   │   ├── dependencies.md     # Module dependency tree
│   │   ├── data-flow.md        # Data flow paths
│   │   ├── hotspots.md         # Complexity hotspots
│   │   └── patterns.md         # Detected code patterns
│   │
│   ├── causation/              # [CAUSAL] Why things are the way they are
│   │   ├── decisions/          # Architecture/design decisions
│   │   ├── tradeoffs/          # Trade-off analyses
│   │   └── migrations/         # Why things changed
│   │
│   ├── episodes/               # [EPISODIC] What happened
│   │   ├── debug/              # Debugging sessions
│   │   ├── decisions/          # Decision conversations
│   │   ├── incidents/          # Production incidents
│   │   ├── learnings/          # New discoveries
│   │   └── failures/           # Failed approaches (very valuable!)
│   │
│   └── meta/                   # [META] Self-knowledge
│       ├── learning-patterns.md    # Strengths, weaknesses, mistakes
│       ├── debugging-heuristics.md # Learned debugging rules
│       ├── confidence-audit.md     # Low-confidence items needing verification
│       └── performance-review.md   # Periodic self-assessment
│
├── raw/                        # Raw sources (IMMUTABLE — agent never modifies)
│   ├── sources/                # Source documents, articles, PDFs
│   └── assets/                 # Images, data files
│
├── cache/                      # Processing cache
│   ├── hashes.json             # SHA256 of source files (for incremental ingest)
│   ├── ingest-queue.json       # Pending ingest tasks
│   └── embeddings/             # Vector embeddings (optional)
│
└── graph/                      # Knowledge graph data
    ├── nodes.json              # Graph nodes (pages)
    ├── edges.json              # Graph edges (relationships)
    └── communities.json        # Detected knowledge clusters
```

---

## System Layers

```
┌─────────────────────────────────────────────────────────┐
│                     USER INTERFACE                       │
│           (Chat, Obsidian, CLI, MCP Server)              │
├─────────────────────────────────────────────────────────┤
│                    OPERATION LAYER                        │
│         INGEST ←→ QUERY ←→ LINT ←→ EVOLVE               │
├─────────────────────────────────────────────────────────┤
│                    MEMORY LAYER                           │
│  Semantic ←→ Episodic ←→ Structural ←→ Causal ←→ Meta   │
├─────────────────────────────────────────────────────────┤
│                   STORAGE LAYER                          │
│        Markdown Files + JSON Cache + Vector DB           │
├─────────────────────────────────────────────────────────┤
│                    SOURCE LAYER                           │
│     Code Files + Git History + Docs + Conversations      │
└─────────────────────────────────────────────────────────┘
```

---

## Data Flow

### Ingest Flow
```
Source → Analyze (Pass 1) → Extract Entities/Relations
       → Generate (Pass 2) → Create/Update Wiki Pages
       → Link → Cross-reference with existing pages
       → Verify → Assign confidence levels
       → Cache → Update SHA256 hashes
       → Log → Append to log.md
```

### Query Flow
```
Question → Classify Intent (what/why/how/history)
         → Search All 5 Memories
         → Rank Results by Relevance + Confidence
         → Synthesize Answer with Citations
         → File Answer (if valuable) → New Wiki Page
         → Log → Record query in log.md
```

### Evolve Flow
```
Session Ends → Extract Lessons
             → Create Episode (if significant)
             → Update Affected Wiki Pages
             → Update Meta-Memory (heuristics, patterns)
             → Propagate → Check dependent pages for staleness
             → Self-Reflect → "What should I do differently?"
```

---

## Integration Points

### With AI Coding Agents

**Claude Code / Codex:**
- Load `.codemind/schema.md` as system context
- Expose wiki operations via MCP server
- Use `.codemind/wiki/index.md` as navigation entry point

**Hermes Agent:**
- Load as a skill (SKILL.md)
- Use cron jobs for periodic lint and evolve
- Store episodes in session memory

**Any Agent (via MCP):**
- MCP server exposes: `ingest`, `query`, `lint`, `evolve`, `search`
- Tools: `read_wiki_page`, `write_wiki_page`, `search_memory`, `get_structure`

### With Development Tools

**Git Hooks:**
- `post-commit` → trigger incremental ingest
- `post-merge` → trigger full lint

**Obsidian:**
- `.codemind/wiki/` is a valid Obsidian vault
- [[wikilinks]] work natively
- Graph view shows knowledge connections
- Dataview plugin can query YAML frontmatter

**IDE Extensions:**
- Hover over code → show related wiki pages
- Inline annotations → show confidence levels
