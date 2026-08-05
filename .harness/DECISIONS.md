# Decisions

## D-001 (2026-08-06): plugin.json `skills` array is an explicit whitelist

- **Decision**: Every shipped skill directory must be listed in `.claude-plugin/plugin.json`'s `skills` array; anything unlisted (drafts, dev scaffolding) does not ship. Empirically confirmed to pass `claude plugin validate --strict` (including as `[]`).
- **Rationale**: Makes shipping intentional — repo content and distributed content can diverge safely.
- **Rejected**: Implicit discovery of `skills/` (no control over what ships).

## D-002 (2026-08-06): Core skill is a single model-invoked skill

- **Decision**: The core capability ships as one `plugin-dev` skill with a trigger-rich description; no slash-command wrappers and no shipped agent definitions.
- **Rationale**: Minimal component count; knowledge single-sourced in one SKILL.md + references; owner prefers natural-language triggering.
- **Rejected**: Core skill + lifecycle commands (`/create`, `/add`, `/review`) — more discoverable but adds components; per-component skill suite — fragments knowledge and raises down-tier navigation risk (C-005).

## D-003 (2026-08-06): Delegation via general-purpose sub-agents with self-contained briefs

- **Decision**: Component authoring and compliance review are dispatched to general-purpose sub-agents using fill-in brief templates kept in `skills/plugin-dev/references/agent-briefs.md`; briefs point the sub-agent at the reference file to read, so the main context never loads authoring references.
- **Rationale**: Satisfies C-003 without shipping and maintaining agent definitions; briefs are self-contained so no conversation context is required.
- **Rejected**: Shipped `component-author`/reviewer agent definitions (extra maintained components, against the minimal-structure direction); authoring in the main context (violates C-003).

## D-004 (2026-08-06): Working-state directory is named after the plugin

- **Decision**: A plugin's cross-step working state lives in `.<plugin-name>/` at the target repo root — for this plugin, `.meta-plugin-creator/plan.md`. The path is a default; the user may override it in conversation. Resume checks the default location first and asks if absent.
- **Rationale**: Name collision safety across plugins and clear ownership of state files; a stable default makes resume and outside inspection mechanical (C-002).
- **Rejected**: A generic shared directory name (e.g. `.plugin-dev/`) — collides when multiple plugins operate on one repo and obscures which tool owns the state.

## D-005 (2026-08-06): Bilingual README as two synchronized files, English canonical

- **Decision**: The repo README ships as `README.md` (English, canonical) plus `README.zh-TW.md` (Traditional Chinese, full synchronized translation): same heading structure, cross-language links near the top, code/commands/identifiers in English in both. Heading-count parity is the executable sync proxy.
- **Rationale**: Owner requested bilingual docs; keeping the English `README.md` canonical preserves the C-007 English-docs convention while the zh-TW file serves the owner's audience.
- **Rejected**: English-only README (ignores the owner's request); one mixed-language file (harder to read and keep in sync); zh-TW as the canonical file (breaks the C-007 docs convention).
