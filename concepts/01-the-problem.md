# The Problem: Why Coding Agents Forget Everything
<!-- concept: the-core-problem -->

## The Goldfish Pattern

Every coding agent today (Claude Code, Cursor, Aider, Copilot) follows this pattern:

```
Session 1: Read codebase → Understand → Solve → FORGET EVERYTHING
Session 2: Read codebase → Understand → Solve → FORGET EVERYTHING
Session 3: Read codebase → Understand → Solve → FORGET EVERYTHING
```

Each session starts from zero. The agent never accumulates knowledge.

## What The Agent Never Learns

Without persistent memory, the agent can't retain:

- **Patterns**: "This codebase uses repository pattern, not active record"
- **Gotchas**: "The auth middleware runs BEFORE error handling — counterintuitive"
- **History**: "We tried approach X last month, it broke because of Y"
- **Preferences**: "The user prefers functional style over OOP"
- **Decisions**: "PostgreSQL was chosen over MongoDB for ACID compliance"
- **Debugging wisdom**: "Intermittent auth errors here usually mean race conditions"

## Why Current Solutions Fail

### RAG (Retrieval-Augmented Generation)
- Re-derives answers from raw documents every time
- No accumulation, no synthesis across documents
- Keyword-based retrieval, not understanding-based

### CLAUDE.md / AGENTS.md / .cursorrules
- Flat text files with no structure
- User must manually write everything
- No self-updating, becomes stale quickly
- No cross-referencing between concepts

### Session Memory
- Lost when session ends
- No integration across sessions
- Can't build compound knowledge

### Vector Databases
- Store embeddings, not understanding
- No cross-referencing or contradiction detection
- No synthesis capability

## What We Actually Need

A coding agent should think like a **senior developer on the team**:

| Capability | Junior Dev | Senior Dev | Current Agent | WllmConcept Agent |
|-----------|-----------|-----------|--------------|----------------|
| Remembers past bugs | ❌ | ✅ | ❌ | ✅ |
| Knows WHY code exists | ❌ | ✅ | ❌ | ✅ |
| Learns from mistakes | Sometimes | ✅ | ❌ | ✅ |
| Understands architecture | Partial | ✅ | Reads it fresh | ✅ (persistent) |
| Gets smarter over time | ✅ | ✅ | ❌ | ✅ |
| Proactively warns about issues | ❌ | ✅ | ❌ | ✅ |

## The Goal

> Transform coding agents from **goldfish with amnesia** to **senior developers with perfect memory**.
