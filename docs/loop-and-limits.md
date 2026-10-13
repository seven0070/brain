# Loop Machinery and Limits

## Main Agent Loop

Streaming agentic cycle with full fallback ladder:

- One retry on connection error before first event
- Mid-stream 529 capacity waits at lower priority
- Stream-to-non-streaming fallback on 404
- Output-filter retry path
- Distinct classification of sleep / lost-connection stalls

## Compaction

Two-tier system:

- **Microcompact**: surgical trimming of individual tool results
- **Auto-compact**: broader reduction when approaching limits

Token-budget audit before deciding what to drop (duplicate file reads, local command outputs, human vs assistant message percentages).

## Additional Controls

- Deferred tool loading (non-deferrable builtins vs deferred vs MCP)
- Thinking token controls (`maxThinkingTokens`) and relay of sub-agent thinking
- Turn classification and resume for interrupted streams
- Model routing across families (Opus, Sonnet, Haiku, internal codenames) and providers

## Quota Limiter

Parses `anthropic-ratelimit-unified-*` response headers into:

- Five-hour window
- Seven-day window

Tracks utilization and whether extra usage is covered.

Near the edge, injects wrap-up instructions into the stream:

- Finish the current step
- Checkpoint
- List up to three remaining-work bullets
- Start no subagents or long work

Designed end-of-session behavior rather than abrupt failure.

## Feature Flags

Internal flags use the `tengu_*` prefix (GrowthBook-backed). Many are dual-gated with environment variables. Used for staged rollout of capabilities such as agent teams, autoDream, background agents, and experimental modes.
