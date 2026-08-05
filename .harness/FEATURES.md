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

## F-002: Bilingual README (en / zh-TW)

- **Behavior**: Repo root ships `README.md` (English, canonical) and `README.zh-TW.md` (Traditional Chinese, synchronized translation): cross-language links near the top, identical 9-heading structure, covering what the plugin is (the five authoring standards), marketplace installation (`wiasliaw/meta-plugin-creator` as its own single-plugin marketplace; install id `meta-plugin-creator@meta-plugin-creator`), `plugin-dev` usage (init/extend modes, `.meta-plugin-creator/plan.md` state file), repo-structure pointer, development toolchain/release flow, and MIT license. Code blocks, commands, paths, and identifiers stay English in both files. A patch changeset (`.changeset/readme-bilingual.md`) records the addition.
- **Verification**:
  1. `claude plugin validate --strict .` — exit 0
  2. `test -f README.md && test -f README.zh-TW.md && echo OK` — prints OK
  3. `grep -q 'README\.zh-TW\.md' README.md && grep -q 'README\.md' README.zh-TW.md && echo OK` — prints OK
  4. `test "$(grep -c '^#' README.md)" -eq "$(grep -c '^#' README.zh-TW.md)" && echo OK` — prints OK
  5. `grep -qF 'wiasliaw/meta-plugin-creator' README.md && grep -qF 'wiasliaw/meta-plugin-creator' README.zh-TW.md && echo OK` — prints OK
  6. `test -n "$(find .changeset -name '*.md' ! -name 'README.md')" && echo OK` — prints OK
- **Evidence**: Reviewer pass verdict 2026-08-06. All six commands re-executed independently by the reviewer and passed (validate exit 0; 9/9 headings in matching order; cross-links, repo references, and changeset present). Content coverage, bilingual sync (zero Simplified-only characters), factual accuracy (license, skill modes, state path, toolchain, install ids all traced to source files), and exclusions (exactly three new files, no excluded path touched) all scored clean with quoted evidence. Work unit: readme-bilingual.
