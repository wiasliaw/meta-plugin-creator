---
name: plugin-dev
description: Use when the user wants to create a new Claude Code plugin, add a skill, command, or agent to an existing plugin, or turn a set of loose skills into a distributable plugin. Guides plugin-level development end to end — interview, plan, scaffold, component authoring via sub-agents, and standards validation. Complements skill-creator, which only covers authoring a single skill.
---

# plugin-dev

Guides development of a Claude Code plugin as a whole: creating one from
scratch, extending an existing one, or turning loose skill directories into a
distributable plugin. This skill is a router — it holds the loop and the
state protocol; every authoring standard lives in `references/`, loaded only
when the current step needs it.

## Worktree safety (read first)

Never hardcode a checkout path or assume `.git` is a directory. At the start
of every session using this skill, resolve the target repo root with:

```
git rev-parse --show-toplevel
```

If that command fails (not a git repo), use the current working directory as
the repo root instead. Use this resolved root for every path below.

## State protocol

All cross-step state lives in one file: `<repo-root>/.meta-plugin-creator/plan.md`.
This is the default location; if the user names a different location in
conversation, use that instead for the rest of the session.

- Read the plan at the start of every step.
- Write the plan at the end of every step — never hold decisions only in
  conversation.
- The plan holds: Purpose, Decisions (dated), a component inventory table
  (columns: component, type, invocation, status), and a dated log.
- Status values: `planned` → `drafted` → `validated`.

Resume check: before starting, check `<repo-root>/.meta-plugin-creator/plan.md`.
If it exists, read it and resume from its component table instead of
re-interviewing. If it is absent, this is a new session — proceed to Mode
dispatch.

## Mode dispatch

1. If no plan file exists → **init** mode.
2. If a plan file exists → **extend** mode.

### Init mode

1. Run the fixed interview (below). Do not invent additional questions.
2. Write the plan file with Purpose, Decisions, and a component inventory
   table built from the interview answers. Mark a row `status: drafted`
   instead of `planned` if that component's skill/command/agent directory
   already exists on disk (e.g. turning loose skills into a plugin) — its
   authoring is already done and only needs the compliance review, not the
   authoring brief.
3. Scaffold the plugin skeleton in the main context (light work, no
   delegation needed): read `references/plugin-structure.md`, then create
   `.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json`, and the
   `skills/` / `commands/` / `agents/` directories the inventory needs.
4. Run the component loop (below) for every `planned` row in the inventory.
5. Run final validation (below).

### Extend mode

1. Read the existing plan file. If the requested addition is not yet a row
   in the component inventory, add a row with `status: planned` and log the
   addition.
2. Run the component loop (below) for every `planned` row.
3. Run final validation (below).

## Fixed interview (init mode only)

Ask exactly these four questions, in order; do not add or skip questions:

1. What is the plugin's purpose — what problem does it solve, in one or two
   sentences?
2. What components does it need — list each skill, command, and agent by
   name and one-line purpose? Include any loose skills that already exist on
   disk and just need to be packaged.
3. For each skill: should it be model-invoked (trigger-rich description) or
   explicitly user-invoked (`disable-model-invocation: true`)? See
   `references/authoring-skills.md` for the distinction.
4. Does the plugin need cross-step state? If yes, what state, and does the
   default `.{plugin-name}/` directory work, or is a different name needed?
   See `references/state-design.md`.

## Component loop

For each inventory row with `status: planned`:

1. Read the plan file's current state.
2. Dispatch one general-purpose sub-agent per component, using the matching
   template in `references/agent-briefs.md` (component-authoring brief).
   Fill in every placeholder — plan excerpt, which reference file to read
   (`references/authoring-skills.md`, `references/authoring-commands.md`, or
   `references/authoring-agents.md`), exact output path, and the constraint
   checklist. Never dispatch with an unfilled placeholder.
3. On the sub-agent's return, update the row to `status: drafted` and append
   a dated log line.

Do not batch multiple components into one sub-agent dispatch — one component
per dispatch keeps briefs self-contained and failures isolated.

## Final validation

1. Read `references/validation.md`.
2. Dispatch one general-purpose sub-agent with the compliance-review brief
   from `references/agent-briefs.md`, pointing it at
   `references/validation.md` and the full constraint list.
3. Run `claude plugin validate --strict .` from the repo root (resolved
   above).
4. On success, set every `drafted` row to `status: validated` in the plan
   file and log the result. On failure, relay the sub-agent's findings, keep
   affected rows at `drafted`, and loop back to the component loop for the
   failing components.

## Reference files

Load only the one the current step names — never load all seven at once:

- `references/plugin-structure.md` — manifest fields, skills whitelist,
  root-as-plugin layout.
- `references/authoring-skills.md` — skill authoring standards and
  invocation-mode choice.
- `references/authoring-commands.md` — command file format, thin-wrapper
  pattern.
- `references/authoring-agents.md` — agent definition format.
- `references/state-design.md` — designing the produced plugin's own state
  filesystem.
- `references/agent-briefs.md` — fill-in sub-agent brief templates.
- `references/validation.md` — running and interpreting
  `claude plugin validate --strict`.

## Out of scope

Auditing an existing plugin against these standards, hooks, MCP components,
release tooling (changesets/CI) for produced plugins, and shipped agent
definitions are not covered by this skill.
