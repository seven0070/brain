# Claude Code Brain

Reverse-engineered design of Claude Code's agentic brain (from March 2026 source leak + shipped binary analysis of v2.1.296, Oct 2026).

## Overview

A constrained, self-healing, bandwidth-aware agent harness optimized for long coding sessions. Core principles:

- Memory is an index, not storage
- Retrieval is skeptical (verify against ground truth)
- Strict write discipline
- Aggressive caching and compaction
- Graceful degradation under quotas

## Architecture Layers

### 1. Memory System (three layers)

| Layer | Content | Lifetime | Loading |
|-------|---------|----------|---------|
| In-context | Conversation, recent tool results, current files | Ephemeral | Always in window |
| Auto-memory | `MEMORY.md` (pointer index) + topic files | Across sessions | Index always loaded; topics on demand |
| Structural | `CLAUDE.md` | Stable / project | Every session start |

**MEMORY.md rules**:
- Lightweight pointers only (~150 chars/line)
- Hard cap ~200 lines / 25 KB
- Write topic file first, then update index
- Treat as hint; verify against codebase
- Transcripts only grepped, never fully loaded

Background consolidation (autoDream) prunes and merges during idle time.

### 2. Prompt Assembly

- Switchable identity plates (CLI / Agent SDK / generic)
- Cache-scoped blocks: global → org → per-session
- Static content first for maximum prefix cache hits
- SHA-256 hashing of blocks for telemetry

### 3. Main Loop

- Streaming agentic loop with full fallback ladder
- Two-tier compaction (microcompact + auto-compact)
- Deferred tool loading
- Thinking token controls
- Turn resume logic

### 4. Coordinator / Worker Orchestration

Persona: "You are Claude Code, an AI assistant that orchestrates software engineering tasks across multiple workers."

**Verbs**: Spawn, Continue (by agent ID), Stop

**Phases**:
1. Research (parallel workers)
2. Synthesis (coordinator only)
3. Implementation (serialized by file set)
4. Verification (independent)

**Rules**:
- Continue when context helps; spawn fresh when it would pollute
- Must synthesize before directing follow-ups ("based on your findings" is anti-pattern)
- Every worker prompt self-contained + purpose statement
- Trust but verify: check actual diffs
- Quote user approvals into worker prompts

Results arrive as `<task-notification>` XML in user-role messages.

### 5. Governance

- Quota limiter via `anthropic-ratelimit-unified-*` headers (5h / 7d)
- Wrap-up instructions near limits (finish step, checkpoint, no new long work)
- Large tool surface (file, shell, Task*, Workflow, Cron, Monitor, MCP, etc.)
- Feature flags: `tengu_*` prefix

## Design Principles

- Keep always-present context small and high-quality
- Make memory inspectable and self-correcting
- Cache aggressively via prefix layout
- Force synthesis and verification in multi-agent work
- Degrade gracefully under resource pressure

---

This is a documentation and reference implementation of the architecture, not Anthropic's proprietary source.
