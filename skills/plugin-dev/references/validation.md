# Validation

## Running the validator

From the repo root (resolved via `git rev-parse --show-toplevel`, cwd
fallback):

```
claude plugin validate --strict .
```

Exit code 0 means the plugin structure and manifest are valid. Run this as
the last step of both init and extend mode, after the component loop and
compliance review.

## Common failures

- **Root CLAUDE.md warning without `marketplace.json`**: running `--strict`
  at a repo root that has a CLAUDE.md but no
  `.claude-plugin/marketplace.json` produces a warning that the root isn't
  recognized as a marketplace-published plugin. Fix: add
  `.claude-plugin/marketplace.json` with a `source: "./"` self-entry, per
  `references/plugin-structure.md`.
- **Whitelist omissions**: a skill directory under `skills/` that is missing
  from `plugin.json`'s `skills` array does not fail `--strict` loudly — it
  silently does not ship. After adding any skill directory, grep
  `plugin.json` for its path to confirm the entry exists; see
  `references/plugin-structure.md`'s whitelist invariant.
- **Line-budget violations**: a `SKILL.md` over 500 lines fails validation.
  Move overflow content into `references/` files (see
  `references/authoring-skills.md`).

## Worktree notes

Always resolve the repo root with `git rev-parse --show-toplevel` before
running the validator, and fall back to the current working directory if
that command fails (not a git repo). Never invoke the validator against a
hardcoded path — a worktree checkout has a different path than the primary
checkout, and the validator must run against whichever one is active.
