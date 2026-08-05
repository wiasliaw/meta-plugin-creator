# Contract: plugin-dev-core-skill

<!--
Purpose: fallback sprint contract for a single feature, used only when the project has not declared a short-term work-unit tool at init. Carries exactly what the agent-flow loop requires before dispatch: Scope, Verification Standards, Exclusions.
Written by: the orchestrator (main session), before dispatching the implementor for this feature. Read by the implementor and reviewer; neither may write to it. While the contract is live, the orchestrator may append dated progress notes below the three sections; closure is moving the file to the archive location.
Location: the fallback location declared in the project's CLAUDE.md Harness section `short-term memory:` bullet.
-->

- **Work-unit identifier**: plugin-dev-core-skill
- **Date**: 2026-08-06

## Scope

Build the core skill of meta-plugin-creator: a single model-invoked skill named
`plugin-dev` that guides plugin-level development of claude-plugins (the gap the
official skill-creator leaves: it covers single skills only, not whole plugins).

### Deliverables

1. `skills/plugin-dev/SKILL.md` — the entry file. Router only: trigger-rich
   frontmatter description (model-invoked; must cover intents "create a new
   claude plugin", "add a skill/command/agent to an existing plugin", "turn
   loose skills into a plugin"), mode dispatch (init | extend), the main loop,
   and the state-file protocol. Target under 150 lines; hard cap 500 (C-008).
2. `skills/plugin-dev/references/` — seven reference files, each loaded on
   demand, never all at once:
   - `plugin-structure.md` — manifest fields, `skills` whitelist invariant
     (every shipped skill dir listed; unlisted does not ship), root-as-plugin
     layout with `.claude-plugin/marketplace.json` (`source: "./"`) so
     `claude plugin validate --strict` passes at repo root.
   - `authoring-skills.md` — skill authoring standards: progressive disclosure
     (lean SKILL.md + references), 500-line cap, explicit user-invoked
     (`disable-model-invocation: true`) vs model-invoked (trigger-rich
     description) choice, description writing guidance.
   - `authoring-commands.md` — command file format; thin-wrapper pattern
     (a command loads one skill, carries no procedure of its own).
   - `authoring-agents.md` — agent definition format: frontmatter (name,
     description, tools), self-contained system prompt.
   - `state-design.md` — designing the produced plugin's state filesystem:
     externalize all cross-step state to declared file paths; default state
     dir named after the plugin (`.<plugin-name>/`); read-at-step-start /
     write-at-step-end protocol; resume from state file.
   - `agent-briefs.md` — fill-in brief templates for dispatching
     general-purpose sub-agents: one for authoring a component, one for the
     final compliance review. Each brief is self-contained (plan excerpt,
     which reference file the sub-agent must read, output paths, constraint
     checklist) and never depends on conversation context.
   - `validation.md` — running `claude plugin validate --strict .`, common
     failures (root CLAUDE.md warning without marketplace.json, whitelist
     omissions), worktree notes.
3. `.claude-plugin/plugin.json` — add `"./skills/plugin-dev"` to the `skills`
   whitelist array.

### Behavior the skill must encode

- **Modes**: init (interview → write plan → scaffold → component loop →
  validate) and extend (ensure plan exists → component loop → validate).
- **Interview**: a fixed question list (plugin purpose, component inventory,
  per-skill invocation model, state needs) — no invented questions.
- **State protocol (C-002)**: all cross-step state lives in
  `.meta-plugin-creator/plan.md` inside the target repo — Purpose, Decisions,
  component inventory table (component, type, invocation, status:
  planned/drafted/validated), dated log. This path is the default; the user
  may override the location in conversation. Resume: check the default
  location first, ask if absent. Every step reads the plan at start and
  updates it at end.
- **Delegation (C-003)**: scaffolding is done in the main context (light
  work); each component is authored by a dispatched general-purpose sub-agent
  using the `agent-briefs.md` templates; a final compliance-review sub-agent
  checks the produced plugin against the authoring standards. The main
  context never loads authoring reference files — briefs point sub-agents at
  them.
- **Worktree safety (C-004)**: all repo paths derived from
  `git rev-parse --show-toplevel` (cwd fallback when not a git repo); never
  assume `.git` is a directory; never reference a primary checkout path.
- **Down-tier support (C-005)**: numbered steps, explicit file paths, fixed
  question lists, fill-in templates; every step names the reference file it
  needs; no "use your judgment" gaps.
- **Standards propagation (C-001/C-002 for outputs)**: plugins produced by
  the skill follow the same standards — lean entries + references, declared
  state layout, explicit invocation model per skill.
- All content in English (C-007), pure markdown, no runtime dependencies
  (C-006).

## Verification Standards

Run from the repo root, in this order:

1. `claude plugin validate --strict .` — exits 0.
2. `test $(wc -l < skills/plugin-dev/SKILL.md) -le 500 && echo OK` — prints OK (C-008).
3. `grep -F '"./skills/plugin-dev"' .claude-plugin/plugin.json` — whitelist entry present.
4. `for f in skills/plugin-dev/references/*.md; do grep -rqF "references/$(basename "$f")" skills/plugin-dev/SKILL.md skills/plugin-dev/references/ --include='*.md' || echo "UNREFERENCED: $f"; done` — prints nothing (every reference file is reachable from SKILL.md or another reference).
5. `ls skills/plugin-dev/references/` — exactly the seven files named in Scope.

Reviewer (non-executable) dimensions:

- SKILL.md is a router: no authoring detail inlined that belongs in a
  reference file.
- Brief templates are self-contained: an agent given only the filled brief
  (no conversation context) could produce the component.
- Frontmatter description is trigger-rich and covers the three intents in
  Scope.
- All files are English, markdown-only, with no runtime dependencies.
- Worktree rule and state protocol are stated explicitly in SKILL.md, not
  implied.

## Exclusions

- **audit mode** (reviewing an arbitrary existing plugin against the
  standards) — deferred to a later work unit.
- **hooks and MCP components** in produced plugins — out of v1 scope.
- **Release tooling for produced plugins** (changesets, CI, version sync) —
  the skill may mention it at wrap-up but does not scaffold it.
- **Shipped agent definitions** (`agents/` in this repo) — delegation uses
  general-purpose sub-agents with briefs; no `component-author` agent ships
  in this unit.
- **Commands** (`commands/` in this repo) — the skill is model-invoked only;
  no slash-command wrappers in this unit.
- **README / usage docs** for this repo — separate unit.
- No changes to `.harness/` files (orchestrator writes them at the merge
  moment only), no changeset file (added at PR time by the orchestrator).
