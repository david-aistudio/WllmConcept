# Why WllmConcept Is Better
<!-- concept: comparison -->

## WllmConcept vs Karpathy

| Dimension | Karpathy | WllmConcept | Why It Matters |
|-----------|----------|----------|---------------|
| Memory Types | 1 (semantic) | 5 (semantic, episodic, structural, causal, meta) | Different knowledge needs different treatment |
| Temporal | No time awareness | Tracks changes over time | Code evolves; wiki must track evolution |
| Code Understanding | Treats code as prose | AST parsing, call graphs, dependency trees | Code has structure, not just text |
| Learning | No learning from experience | Learns from every session | Gets smarter, not just bigger |
| Confidence | All knowledge equal | VERIFIED → INFERRED → ASSUMED | Know what to trust |
| Causal | No "why" knowledge | Captures design decisions | Prevents "fixing" intentional choices |
| Self-Improvement | No self-improvement | Meta-memory tracks patterns | Agent learns from its mistakes |
| Proactive | Waits for human input | Auto-detects staleness, gaps | Agent helps maintain itself |
| Code Sync | No code awareness | Auto-syncs with code changes | Wiki stays accurate |
| Debugging | No debugging support | Episodes + heuristics | Faster bug resolution over time |
| Verification | No verification | Confidence scoring + auto-verify | Trust but verify |
| Scalability | Manual maintenance | Auto-lint, auto-evolve | Works at scale |

**The Fundamental Difference:**

Karpathy's wiki = **Library** (organized, cross-referenced, passive)
WllmConcept = **Brain** (perceives, learns, reasons, reflects, evolves)

---

## WllmConcept vs Current Coding Agents

### Claude Code
| Aspect | Claude Code | WllmConcept |
|--------|------------|----------|
| Memory | CLAUDE.md (flat file) | 5 structured memory systems |
| Learning | None | Episodic + Meta memory |
| Code understanding | Reads files fresh | Persistent structural memory |
| Cross-session | None | Full continuity |
| Self-improvement | None | Reflection after every session |

### Cursor / Windsurf
| Aspect | Cursor | WllmConcept |
|--------|--------|----------|
| Memory | .cursorrules (static text) | Self-updating wiki |
| Learning | None | Learns from debugging |
| Code understanding | Good indexing, no memory | Persistent + evolving |
| Rules | User writes manually | Agent maintains automatically |

### Aider
| Aspect | Aider | WllmConcept |
|--------|-------|----------|
| Memory | Per-session only | Persistent across sessions |
| Code understanding | Repo map (good) | Structural memory (better) |
| Git awareness | Yes | Yes + causal memory |
| Learning | None | Full learning system |

### Letta (MemGPT)
| Aspect | Letta | WllmConcept |
|--------|-------|----------|
| Memory | Tiered (core, archival, recall) | 5 specialized types |
| Code specific | Generic | Code-aware structural memory |
| Self-editing | Yes | Yes + reflection + evolution |
| Causal | No | Yes (design decisions) |

---

## State of the Art: What We Learn From Others

| System | What We Take | What We Improve |
|--------|-------------|-----------------|
| **Karpathy LLM Wiki** | Persistent wiki, cross-references, LLM-as-maintainer | Add 4 memory types, confidence, temporal |
| **Letta/MemGPT** | Self-editing memory, tiered storage | Make code-specific |
| **Stanford Generative Agents** | Reflection mechanism | Make faster and code-specific |
| **Sourcegraph** | Code graph (AST, call graph) | Add semantic + causal layers |
| **Reflexion** | Self-improvement from failures | Make continuous, not just on-failure |
| **nashsu/llm_wiki** | Two-pass ingest, knowledge graph | Add episodic + causal + meta |
| **Aider** | Repo map, git awareness | Add persistent learning |
