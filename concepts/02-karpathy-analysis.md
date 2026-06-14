# Karpathy's LLM Wiki: Analysis
<!-- concept: karpathy-analysis -->

## What Karpathy Proposed

Andrej Karpathy's [LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) is a pattern for building personal knowledge bases:

**Core idea:** Instead of RAG (re-derive from raw docs every time), build a **persistent wiki** that the LLM maintains and the human curates. Knowledge compounds over time.

**Architecture:** Three layers:
1. **Raw Sources** — immutable source documents
2. **Wiki** — LLM-generated markdown pages with cross-references
3. **Schema** — rules for how the LLM maintains the wiki

**Operations:**
1. **Ingest** — LLM reads source, creates wiki pages, cross-references
2. **Query** — LLM searches wiki, synthesizes answer with citations
3. **Lint** — LLM checks for contradictions, staleness, orphans

## What's Brilliant (KEEP These)

### 1. Persistent Compounding Knowledge
The wiki gets richer with every source. Knowledge COMPOUNDS instead of evaporating. This is the key insight.

### 2. LLM-as-Maintainer, Human-as-Curator
Perfect division of labor:
- **Human:** Chooses WHAT to learn, asks questions, makes decisions
- **LLM:** Does the HOW — summarizing, cross-referencing, filing, bookkeeping

### 3. Pre-Built Cross-References
When you query, connections are ALREADY THERE. No need to discover them at query time. Saves tokens and time.

### 4. Contradiction Detection
When new info conflicts with old, the LLM flags it. Prevents knowledge drift.

### 5. Three-Layer Architecture
Clean separation: Raw Sources → Wiki → Schema.

### 6. Index + Log
- `index.md` — content catalog for navigation
- `log.md` — chronological record of operations

### 7. [[Wikilink]] Syntax
Simple, Obsidian-compatible cross-referencing.

## What's Missing for Coding Agents (8 Gaps)

### Gap 1: No Temporal Dimension
Karpathy's wiki knows what IS, not what WAS or what CHANGED.
- Coding needs: "This function changed 3 times. Here's why."
- Coding needs: "The old approach was X, we switched to Y because of Z."

### Gap 2: No Code Structure Awareness
Treats code as prose. Doesn't understand:
- AST (Abstract Syntax Tree)
- Call graphs (who calls whom)
- Dependency trees (what imports what)
- Data flow paths

### Gap 3: No Episodic Memory
Doesn't remember past sessions:
- "Last time I debugged this, the issue was in config, not code"
- "Approach A failed because of X, try approach B instead"

### Gap 4: No Confidence/Verification
Treats all knowledge equally. No way to distinguish:
- Verified fact vs assumption
- High confidence vs guess

### Gap 5: No Proactive Discovery
Waits for human to add sources. Never:
- Scans for code changes automatically
- Detects documentation drift
- Suggests knowledge gaps to fill

### Gap 6: No Self-Improvement
Never asks:
- "How could I have solved that faster?"
- "What pattern did I miss?"
- "What should I do differently next time?"

### Gap 7: No Causal Knowledge
Knows WHAT, not WHY:
- "Auth uses JWT" (what) vs "Auth uses JWT because OAuth was too complex and the team voted in sprint 14" (why)
- Causal knowledge separates senior devs from juniors

### Gap 8: No Multi-Scale Knowledge
Only one level of detail. Coding needs:
- **Bird's eye:** "Microservices e-commerce app"
- **Mid-level:** "Auth service handles JWT tokens"
- **Detail:** "validateToken() checks expiry, signature, issuer"
- **Micro:** "Line 42 has a race condition because..."

## The Enhancement Strategy

We keep everything brilliant about Karpathy and fill every gap:

```
Karpathy (keep)           WllmConcept (add)
─────────────────────     ─────────────────────────
Semantic Memory      →    Semantic Memory (enhanced with confidence)
                     +    Episodic Memory (past sessions)
                     +    Structural Memory (code understanding)
                     +    Causal Memory (design decisions)
                     +    Meta-Memory (self-improvement)
Ingest               →    Ingest (enhanced: two-pass, structural extraction)
Query                →    Query (enhanced: multi-memory, confidence-weighted)
Lint                 →    Lint (enhanced: code-sync, confidence audit)
                     +    Evolve (new: self-reflection and learning)
```
