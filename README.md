# meta-plugin-creator

**English** | [繁體中文](./README.zh-TW.md)

A Claude Code plugin that creates Claude Code plugins.

## What It Is

`meta-plugin-creator` encodes a set of authoring standards and guides you
through producing plugins that satisfy them. Every plugin it helps you build
— and this plugin itself — follows five standards:

- **Progressive disclosure** — a lean entry file (`SKILL.md`, command) that
  defers detail to reference files loaded on demand.
- **State externalization** — cross-step state lives in a declared file, not
  only in conversation context.
- **Delegation to sub-agents** — heavy or noisy work is delegated to
  sub-agents rather than run in the main context.
- **Git-worktree compatibility** — plugin logic never assumes `.git` is a
  directory or hardcodes the primary checkout path.
- **Down-tier model support** — instructions are explicit enough to be
  executed correctly down to the Opus model tier, with no reliance on
  unstated conventions.

## Installation

This repository is its own single-plugin marketplace
(`.claude-plugin/marketplace.json`). Add the marketplace from GitHub, then
install the plugin.

From within a Claude Code session:

```
/plugin marketplace add wiasliaw/meta-plugin-creator
/plugin install meta-plugin-creator@meta-plugin-creator
```

Or with the `claude` CLI:

```
claude plugin marketplace add wiasliaw/meta-plugin-creator
claude plugin install meta-plugin-creator@meta-plugin-creator
```

## Usage

The plugin ships one skill, `plugin-dev`. It ships no slash commands —
it is model-invoked: Claude Code triggers it automatically when you ask to
create a new plugin, add a skill/command/agent to an existing plugin, or turn
a set of loose skills into a distributable plugin.

`plugin-dev` runs in one of two modes, chosen automatically:

### Init Mode

Used when no plan file exists yet for the target repo. Runs a fixed
four-question interview, writes a plan file, scaffolds the plugin skeleton
(`.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json`, and the
needed `skills/` / `commands/` / `agents/` directories), dispatches one
sub-agent per planned component to author it, then runs final validation.

### Extend Mode

Used when a plan file already exists. Adds the requested component to the
plan's component inventory, dispatches a sub-agent to author it, then runs
final validation.

Cross-step working state lives in `.meta-plugin-creator/plan.md` in the
target repo by default; you can tell the skill to use a different location.

## Repository Structure

A brief overview — see `CLAUDE.md` for the full tree:

- `.claude-plugin/` — plugin manifest and marketplace listing.
- `skills/plugin-dev/` — the `plugin-dev` skill and its reference files.
- `.changeset/` — changeset entries for release tooling.
- `.harness/` — long-term project memory (architecture, constraints,
  decisions, verified features).

## Development

- Toolchain: Node.js 24.x + pnpm 10.x, used only for release tooling — the
  plugin itself is pure markdown with no runtime dependencies.
- Verify: `claude plugin validate --strict .`
- Release: changesets drive versioning. On push to `main`, a GitHub Action
  opens a release PR; merging it tags and publishes a GitHub Release.
  Distribution is this git repository itself — there is no npm publish.

## License

MIT — see [LICENSE](./LICENSE).
