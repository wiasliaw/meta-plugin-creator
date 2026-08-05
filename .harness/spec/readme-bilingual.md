# Contract: readme-bilingual

<!--
Purpose: fallback sprint contract for a single feature, used only when the project has not declared a short-term work-unit tool at init. Carries exactly what the agent-flow loop requires before dispatch: Scope, Verification Standards, Exclusions.
Written by: the orchestrator (main session), before dispatching the implementor for this feature. Read by the implementor and reviewer; neither may write to it. While the contract is live, the orchestrator may append dated progress notes below the three sections; closure is moving the file to the archive location.
Location: the fallback location declared in the project's CLAUDE.md Harness section `short-term memory:` bullet.
-->

- **Work-unit identifier**: readme-bilingual
- **Date**: 2026-08-06

## Scope

Create two synchronized README files at the repo root:

- `README.md` — English, the canonical version (keeps the C-007 English-docs convention).
- `README.zh-TW.md` — Traditional Chinese, a full translation kept structurally in sync with the English version (same section order, same heading count, equivalent content). User-requested bilingual delivery.

Both files must:

1. Link to the other language version near the top (a language-switcher line).
2. Cover, in this order or a close equivalent:
   - What the plugin is: a claude-plugin that creates claude-plugins, encoding the five authoring standards (progressive disclosure, state externalization, delegation to sub-agents, git-worktree compatibility, down-tier model support down to Opus).
   - Installation: the repo is its own single-plugin marketplace (`.claude-plugin/marketplace.json`); show `claude` CLI / in-session commands to add the marketplace from this GitHub repo (`wiasliaw/meta-plugin-creator`) and install the `meta-plugin-creator` plugin.
   - Usage: the `plugin-dev` skill is model-invoked via natural language (no slash commands shipped, per D-002); describe init mode (fixed interview → plan file → scaffold → per-component sub-agent dispatch → validation) and extend mode (component loop on an existing plugin); note cross-step working state lives in `.meta-plugin-creator/plan.md` in the target repo (default, user-overridable, per D-004).
   - Repo structure: a brief overview; may point to CLAUDE.md for the full tree.
   - Development: toolchain (Node.js 24.x + pnpm 10.x for release tooling only; plugin itself is pure markdown), verification command (`claude plugin validate --strict .`), release flow (changesets → GitHub Release; distribution is the git repo itself, no npm publish).
   - License: state the license found in `LICENSE`.
3. Derive every fact (names, paths, commands, license type, version) from the repo's actual files — `.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json`, `CLAUDE.md`, `skills/plugin-dev/SKILL.md`, `LICENSE`, `package.json` — never invent or assume.
4. Keep code blocks, commands, file paths, and identifiers in English in both versions; the zh-TW version translates prose only.

Also add one changesets entry (a manually written markdown file under `.changeset/`, patch bump for `meta-plugin-creator`) describing the README addition, per the declared release workflow.

## Verification Standards

Run from the repo root; all must pass:

1. `claude plugin validate --strict .` — exit 0.
2. `test -f README.md && test -f README.zh-TW.md && echo OK` — prints OK.
3. `grep -q 'README\.zh-TW\.md' README.md && grep -q 'README\.md' README.zh-TW.md && echo OK` — prints OK (cross-language links present).
4. `test "$(grep -c '^#' README.md)" -eq "$(grep -c '^#' README.zh-TW.md)" && echo OK` — prints OK (identical heading structure, the sync proxy).
5. `grep -qF 'wiasliaw/meta-plugin-creator' README.md && grep -qF 'wiasliaw/meta-plugin-creator' README.zh-TW.md && echo OK` — prints OK (install instructions reference the real repo).
6. `test -n "$(find .changeset -name '*.md' ! -name 'README.md')" && echo OK` — prints OK (changeset entry exists).

## Exclusions

- No changes to plugin source (`skills/`, `.claude-plugin/`), `CLAUDE.md`, `.harness/`, `.github/`, or `scripts/`.
- No language versions beyond English and Traditional Chinese.
- No version bump or release execution — only the changeset file; the release PR handles versioning.
- No CI additions (e.g. no README-sync check workflow).
- No badges that depend on services not already in use.
