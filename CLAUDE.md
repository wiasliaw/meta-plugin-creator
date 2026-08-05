# meta-plugin-creator

A claude-plugin that creates claude-plugins: it encodes the authoring
standards in `.harness/CONSTRAINTS.md` and produces plugins that satisfy them.

Load the `harness:agent-flow` skill before producing or modifying any deliverable — code, docs, or configuration.

## Repo Structure

```tree
meta-plugin-creator/
├── .claude-plugin/
│   ├── plugin.json          # plugin manifest; `skills` array whitelists shipped skill dirs; version synced from package.json
│   └── marketplace.json     # repo as its own single-plugin marketplace; lets `--strict` pass at repo root
├── skills/                  # (planned) skill content: SKILL.md entry + references/ per skill
├── commands/                # (planned) slash-command entry points
├── agents/                  # (planned) sub-agent definitions
├── .changeset/              # changesets config (changelog via @changesets/changelog-github)
├── .github/workflows/release.yml   # changesets action: release PR on push to main, then tag + GitHub Release
├── .harness/                # long-term memory; git-tracked; merge-moment writes only
│   ├── ARCHITECTURE.md
│   ├── CONSTRAINTS.md
│   ├── DECISIONS.md
│   ├── FEATURES.md
│   └── spec/                # finalized sprint-contract specs, archived at merge moments
├── .project/                # short-term memory; gitignored; created on first work unit
│   ├── contracts/           # in-flight sprint-contract per work unit
│   └── handoff.md           # session handoff
├── CLAUDE.md
├── LICENSE
├── package.json             # release tooling only (pnpm + changesets); its version is the source of truth
└── scripts/sync-plugin-version.mjs  # copies package.json version into plugin.json; `--check` mode for CI
```

## Development Environment

- Toolchain: Node.js 24.x + pnpm 10.x (release tooling only — the plugin itself is pure markdown); Claude Code CLI (plugin validation).
- External services and environment variables: GitHub (hosting; Actions will drive the release flow). No environment variables or secrets are required.

## Version Control

This project uses GitHub Flow — each change happens on a short-lived feature branch and merges into `main` via pull request, so `main` only ever receives verified work.

## Workflow

| Phase | How |
|---|---|
| spec | Manual: write a sprint-contract — Scope, Verification Standards, Exclusions — to `.project/contracts/<unit>.md`. |
| design | Manual: design the plugin structure (skills/commands/agents split, state-file layout) against `.harness/CONSTRAINTS.md`; significant choices are recorded in `.harness/DECISIONS.md` at the merge moment. |
| build | Manual editing, or run `/harness:agent-flow` for the orchestrated implementor/reviewer loop. |
| verify | `claude plugin validate --strict .` |
| release | `pnpm changeset` per work unit. On push to `main`, the changesets GitHub Action (`.github/workflows/release.yml`) opens a release PR via `pnpm run version` (version bump + plugin.json sync); merging it tags and publishes a GitHub Release. Distribution is this git repo itself (marketplaces point here); no npm publish. |

## Harness

- harness plugin version: v0.2.1
- `.harness/ARCHITECTURE.md` — module map, layering, key boundaries; read before making a structural change.
- `.harness/CONSTRAINTS.md` — hard MUST / MUST NOT rules with source and applicability; read before any change that could violate one.
- `.harness/DECISIONS.md` — append-only decision log; read before revisiting a past call.
- `.harness/FEATURES.md` — passing-only registry of verified behavior; read to see what already works and how it was verified.
- `.harness/spec/` — finalized sprint-contract specs, one per completed work unit; archived here by the orchestrator at the merge moment.
- short-term memory: fallback sprint-contract files at `.project/contracts/<unit>.md` (Scope, Verification Standards, Exclusions), one per in-flight work unit.
- session memory: `.project/handoff.md`
- `.harness/` is read-anytime and written only by the orchestrator at merge moments — see the harness-agent-flow skill (or run `/harness:agent-flow`) for the loop that enforces this.
