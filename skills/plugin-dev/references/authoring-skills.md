# Authoring skills

## Progressive disclosure (C-001)

A skill is `SKILL.md` (the entry file) plus an optional `references/`
directory. The entry file must stay lean: router logic, the numbered steps,
and pointers to reference files. Any content that is authoring detail,
background, or an explanation only needed for one step belongs in a
reference file, loaded on demand — never inlined into the entry file "just
in case."

## Line budget (C-008)

`SKILL.md` MUST NOT exceed 500 lines. Treat 500 as a hard backstop, not a
target — a well-scoped entry file is usually well under 150 lines for a
single-purpose skill. If a draft approaches the cap, move detail out to a
new or existing reference file rather than trimming prose to fit.

## Invocation mode: model-invoked vs explicit

Every skill picks one of two invocation modes:

- **Model-invoked** (default): Claude Code decides to load the skill based
  on its frontmatter `description` matching the user's request. The
  description must be trigger-rich: name the concrete situations, user
  phrasings, and intents that should load it, not just an abstract category.
  Write it as "Use when X, Y, or Z" with the actual triggers spelled out.
- **Explicit / user-invoked**: add `disable-model-invocation: true` to the
  frontmatter. Claude Code will only load this skill when the user runs
  `/<plugin>:<skill-name>` directly, never by inference. Choose this when
  the skill's behavior is destructive, expensive, or narrow enough that
  automatic triggering would misfire.

Ask which mode a skill needs during the interview (`SKILL.md`'s fixed
interview, question 3) — do not default silently.

## Writing the description field

The `description` frontmatter field is the only signal Claude Code uses to
decide whether to load a model-invoked skill. It must:

- Name concrete user intents and phrasings ("Use when the user asks to X" or
  "Use when Y is about to happen"), not just a topic label.
- State what the skill does once loaded, briefly.
- Avoid vague single-word triggers ("plugins", "skills") that could match
  unrelated requests.

## Frontmatter shape

```yaml
---
name: <skill-name>
description: <trigger-rich description>
---
```

Add `disable-model-invocation: true` only for explicit-invocation skills.
