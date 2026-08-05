# Authoring agents

## Format

An agent definition is a single markdown file at `agents/<agent-name>.md`
with frontmatter:

```yaml
---
name: <agent-name>
description: <when to dispatch this agent, and what it does>
tools: <comma-separated tool list, or omit for all tools>
---
```

- `name` — how the agent is addressed when dispatched.
- `description` — written for the dispatcher, not the agent: state when to
  use this agent and what it returns, since the dispatching skill/session
  reads this to decide whether to spawn it.
- `tools` — restrict to only what the agent's task needs (e.g.
  `Read, Grep, Glob` for a read-only reviewer). Omit the field to grant all
  tools.

## Self-contained system prompt

The body of the file is the agent's system prompt. It must be
self-contained: an agent instance starts with no memory of the conversation
that dispatched it, so the prompt cannot reference "the plan we discussed"
or "the file I mentioned" — every fact the agent needs (paths, constraints,
output format) must either be in this file or in the per-dispatch brief the
caller fills in (see `references/agent-briefs.md`).

Write the prompt as: role/purpose, inputs it should expect in its dispatch
message, the steps to perform, and the exact report format to return.
