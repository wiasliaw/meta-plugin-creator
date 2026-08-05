# Plugin structure

## Manifest: `.claude-plugin/plugin.json`

Fields:

- `name` — plugin name, kebab-case.
- `version` — semver string (may be a placeholder such as `0.0.0` if release
  tooling manages versioning separately).
- `description` — one or two sentences; shown in marketplace listings.
- `author` — `{ "name": ..., "email": ... }`.
- `skills` — an array of relative paths to shipped skill directories (see
  whitelist invariant below).

Optional: `commands`, `agents` — only needed if the plugin ships components
outside the default `commands/` / `agents/` directory names. If every
command/agent lives in the standard directory, Claude Code auto-discovers
them; the explicit whitelist is required for `skills` regardless.

## Skills whitelist invariant

Every skill directory shipped inside `skills/` MUST appear as an entry in
`plugin.json`'s `skills` array, e.g. `"./skills/plugin-dev"`. A skill
directory that exists on disk but is missing from this array does NOT ship —
Claude Code will not load it, and `claude plugin validate --strict` will not
surface a missing entry as an error, so this is a silent failure. Always
check the array after adding a new skill directory (see
`references/validation.md`'s whitelist-omission note).

## Root-as-plugin layout

A repo can serve simultaneously as a plugin's source and its own
single-plugin marketplace. This lets `claude plugin validate --strict .` run
at the repo root without a separate marketplace repo.

Required for this layout:

- `.claude-plugin/plugin.json` — the plugin manifest, at repo root.
- `.claude-plugin/marketplace.json` — declares the repo as a marketplace
  listing itself:

```json
{
  "name": "<plugin-name>",
  "owner": { "name": "...", "email": "..." },
  "description": "...",
  "plugins": [
    {
      "name": "<plugin-name>",
      "source": "./",
      "description": "..."
    }
  ]
}
```

The `source: "./"` entry is what tells the validator this repo is both the
plugin and the marketplace pointing at it. Without `marketplace.json`,
`claude plugin validate --strict` at repo root emits a warning about the root
CLAUDE.md not being recognized as part of a marketplace-published plugin —
see `references/validation.md` for the exact message and fix.

## Directory layout

```
<repo-root>/
├── .claude-plugin/
│   ├── plugin.json
│   └── marketplace.json
├── skills/<skill-name>/
│   ├── SKILL.md
│   └── references/
├── commands/<command-name>.md
└── agents/<agent-name>.md
```

Only create the directories the component inventory actually needs — an
empty `commands/` or `agents/` directory is not required if the plugin ships
no components of that type.
