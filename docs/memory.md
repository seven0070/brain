# Memory System

## Three Layers

### Layer 1: In-Context Memory
- Active conversation history
- Recent tool outputs
- Current file being edited
- Ephemeral: cleared or compacted between sessions
- Subject to microcompact and auto-compact

### Layer 2: Auto-Memory (`MEMORY.md` + topic files)

Location pattern: `~/.claude/projects/<project>/memory/`

**MEMORY.md (index)**:
- Always loaded into context at session start
- Contains only short pointers (~150 characters per line)
- Hard limits: ~200 lines or 25 KB (truncated if exceeded)
- Never stores actual content

**Topic files** (examples):
- `project-context.md`
- `decisions.md`
- `code-patterns.md`
- `user-preferences.md`

Loaded on demand when the index indicates relevance.

**Strict write discipline**:
1. Write or rewrite the topic file
2. Only after success, update the pointer in MEMORY.md

**Self-healing**:
- Memory treated as a hint
- Verify against actual codebase before acting
- Rewrite outdated or contradictory entries
- Code-derived facts often not stored (re-derived instead)

**Transcripts**:
- Never fully reloaded into context
- Only selectively grepped for specific identifiers

### Layer 3: Structural Project Memory (`CLAUDE.md`)
- Human/team-authored
- Loaded at every session start
- Contains: architecture, coding standards, off-limits paths, build/test commands, project goals
- Stable relative to auto-memory

## Background Consolidation (autoDream)

Runs during idle time under triple gates:
- ≥ 24 hours since last run
- ≥ 5 new sessions
- No active lock file

Four phases:
1. Orient — inventory current state
2. Gather — collect recent signals
3. Consolidate — merge, deduplicate, resolve contradictions, convert vague → absolute
4. Prune & Index — keep MEMORY.md under hard limits

Typically runs in a forked sub-agent with restricted tools.

## Design Properties
- Token-efficient (selective loading)
- Transparent (plain Markdown, human-editable)
- Survives context resets
- Degrades gracefully
