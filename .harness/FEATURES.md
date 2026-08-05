# Features

## F-001: plugin-dev core skill

- **Behavior**: Model-invoked `plugin-dev` skill guides plugin-level development of claude-plugins: init mode (fixed 4-question interview → plan file → scaffold → per-component sub-agent dispatch → validation) and extend mode (component loop on an existing plugin). Cross-step state lives in `.meta-plugin-creator/plan.md` in the target repo (default, user-overridable); component authoring and final compliance review are dispatched to general-purpose sub-agents via self-contained brief templates; repo paths derive from `git rev-parse --show-toplevel` (worktree-safe).
- **Verification**:
  1. `claude plugin validate --strict .` — exit 0
  2. `test $(wc -l < skills/plugin-dev/SKILL.md) -le 500 && echo OK` — prints OK
  3. `grep -F '"./skills/plugin-dev"' .claude-plugin/plugin.json` — matches
  4. `for f in skills/plugin-dev/references/*.md; do grep -rqF "references/$(basename "$f")" skills/plugin-dev/SKILL.md skills/plugin-dev/references/ --include='*.md' || echo "UNREFERENCED: $f"; done` — no output
  5. `ls skills/plugin-dev/references/` — exactly the seven declared reference files
- **Evidence**: Reviewer pass verdict 2026-08-06. All five commands re-executed independently by the reviewer: validate exit 0, SKILL.md 140 lines (≤ 500), whitelist entry present, no unreferenced reference files, seven files exact. All five reviewer dimensions scored clean with quoted evidence; all Exclusions respected. Work unit: plugin-dev-core-skill.
