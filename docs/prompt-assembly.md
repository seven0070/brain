# Prompt Assembly and Caching

## Identity Plates

Three switchable identity sentences embedded in the binary, selected by context:

- Interactive CLI: "You are Claude Code, Anthropic's official CLI for Claude."
- Claude Agent SDK variant
- Generic agent variant

Selection depends on mode (interactive, non-interactive, Vertex, appended system prompt, etc.).

## Cache-Scoped Blocks

Prompt is assembled from blocks tagged with cache scopes:

- **Global / static** — shared across users (persona, core rules, tool guidance)
- **Org-scoped** — shared within an organization
- **Per-session** — unique to the current run

## Layout for Caching

Static content first so the longest possible prefix is cacheable:

1. Static system prompt + tools (globally shareable)
2. CLAUDE.md (project-scoped)
3. Session context
4. Conversation messages

Each block can be SHA-256 hashed for telemetry.

## Why This Matters

Prefix matching means any change in the early part of the prompt invalidates everything after it. Ordering maximizes shared cache hits across users and sessions while keeping dynamic content after the breakpoints.

## Main Interactive Prompt Structure

Ordered sections typically include:
- Tone and style
- How to do tasks
- Plan-mode workflow
- Session context
- User-declared rules
- Tool-specific guidance (including "avoid rabbit holes and loops")
