# Sub-agent brief templates

Every dispatch to a general-purpose sub-agent from this skill uses one of
the two templates below. Fill in every `<placeholder>` before dispatching —
a brief with an unfilled placeholder is not ready to send. A sub-agent has
no memory of the conversation that dispatched it: every fact it needs must
be in the filled brief.

## Template A — Component authoring

Use once per component in the component loop.

```
You are authoring one component of the Claude Code plugin at <repo-root>.

Plan excerpt (do not re-interview; use this as ground truth):
<paste the relevant plan.md rows and Purpose/Decisions text>

Component to build:
- Name: <component-name>
- Type: <skill | command | agent>
- Invocation: <model-invoked | explicit | n/a for commands/agents>
- One-line purpose: <purpose from the plan>

Before writing anything, read the authoring standard:
<repo-root>/skills/plugin-dev/references/<authoring-skills.md |
authoring-commands.md | authoring-agents.md — pick the one matching Type>.

Output path: <exact target path, e.g. skills/<name>/SKILL.md,
commands/<name>.md, agents/<name>.md>

Constraint checklist (verify each before returning):
- [ ] C-001 progressive disclosure: entry file lean, detail in references if
      any.
- [ ] C-002 state externalization: any cross-step state in this component
      uses a declared file path, per references/state-design.md, not
      conversation-only.
- [ ] C-005 down-tier support: numbered steps, explicit paths, no "use your
      judgment" gaps.
- [ ] C-006 pure markdown, no runtime dependencies.
- [ ] C-007 all content in English.
- [ ] If a skill: C-008 SKILL.md ≤ 500 lines.

Report back: file(s) written, and a one-line confirmation of each checklist
item.
```

## Template B — Final compliance review

Use once, after the component loop, before running
`claude plugin validate --strict`.

```
You are reviewing the plugin at <repo-root> against its authoring standards,
before final validation.

Read <repo-root>/skills/plugin-dev/references/validation.md first.

Check every component listed in
<repo-root>/.meta-plugin-creator/plan.md (or the user-declared state file
location) against:
- C-001 progressive disclosure
- C-002 state externalization
- C-003 delegation (heavy/noisy work is not inlined into a skill's main
  context)
- C-004 worktree compatibility (no hardcoded checkout path, no assumption
  `.git` is a directory)
- C-005 down-tier support
- C-006 pure markdown, no runtime dependencies
- C-007 English content
- C-008 SKILL.md ≤ 500 lines

Report back: a pass/fail per component per constraint, with the file and
line for any failure.
```
