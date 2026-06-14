# CodeMind
## Cognitive Architecture for AI Coding Agents

> A coding agent should have a **brain**, not just a **notebook**.

CodeMind is a knowledge system that makes AI coding agents learn, remember, and improve over time — like a senior developer who never forgets.

Based on [Karpathy's LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f), extended with 4 new memory types specifically designed for code.

---

## Quick Navigation

| Document | Purpose |
|----------|---------|
| [concepts/01-the-problem.md](concepts/01-the-problem.md) | Why coding agents forget everything |
| [concepts/02-karpathy-analysis.md](concepts/02-karpathy-analysis.md) | What's brilliant and what's missing in Karpathy's idea |
| [concepts/03-five-memories.md](concepts/03-five-memories.md) | The 5 memory types every coding agent needs |
| [concepts/04-why-better.md](concepts/04-why-better.md) | Comparison: CodeMind vs Karpathy vs Current Agents |
| [specs/architecture.md](specs/architecture.md) | Technical architecture and file structure |
| [specs/schema.md](specs/schema.md) | Configuration schema (config.yaml) |
| [specs/frontmatter.md](specs/frontmatter.md) | Wiki page metadata format |
| [workflows/ingest.md](workflows/ingest.md) | How knowledge enters the system |
| [workflows/query.md](workflows/query.md) | How questions are answered |
| [workflows/lint.md](workflows/lint.md) | How the wiki stays healthy |
| [workflows/evolve.md](workflows/evolve.md) | How the agent improves itself |
| [examples/debug-session.md](examples/debug-session.md) | Real debugging scenario walkthrough |
| [examples/onboarding.md](examples/onboarding.md) | New codebase onboarding walkthrough |

---

## The One-Liner

**Karpathy:** Build a wiki from your sources. LLM maintains it. You curate it.
**CodeMind:** Build a *brain* from your code. LLM learns from it. It gets smarter.

---

## Core Principles

1. **Knowledge compounds.** Every session adds to the system. Nothing is lost.
2. **Memory has types.** Facts, experiences, structure, decisions, and self-knowledge are different.
3. **Confidence is tracked.** Not all knowledge is equal. Verified > Inferred > Assumed.
4. **Time matters.** What was true yesterday might not be true today.
5. **The agent reflects.** It learns from its own mistakes and improves.
