# Architecture

## Module Map

- `.claude-plugin/` — plugin manifest (`plugin.json`): name, version, metadata. (planned — not yet built)
- `skills/` — skill content; each skill is a `SKILL.md` entry point plus `references/` detail files. Currently ships `plugin-dev`, the core plugin-authoring skill (router SKILL.md + seven references).
- `commands/` — slash-command entry points; thin wrappers that load a skill. (planned — none yet; the core skill ships without wrappers per D-002)
- `agents/` — sub-agent definitions used to delegate heavy work out of the main context. (planned — none yet; delegation uses general-purpose sub-agents with briefs per D-003)
- `.harness/` — long-term memory: architecture, constraints, decisions, verified features, archived specs.
- `.project/` — short-term memory: in-flight sprint-contracts and the session handoff file.

## Layering & Dependency Direction

- commands → skills → references — a command is a thin entry point that loads one skill; `SKILL.md` carries the procedure; detail lives in reference files loaded on demand (progressive disclosure).
- skills → agents — a skill may dispatch sub-agents or a claude-team for heavy work; agents receive self-contained briefs and never pull the full skill back into the main context.
- plugin content → state filesystem — all cross-step state flows through declared file paths (`.project/`, `.harness/`), never through conversation context alone.

## Key Boundaries

- `.harness/` — written only by the orchestrator at merge moments; read anytime. Implementor/reviewer agents never write it.
- `.project/` — short-term state only; no long-term fact may live only here.
- Plugin source (`skills/`, `commands/`, `agents/`) — pure English markdown with no runtime dependencies; must be executable by models down to Opus.
- Repo paths — all path logic must hold inside a git worktree: never assume `.git` is a directory; derive paths from the working directory, not the primary checkout.
