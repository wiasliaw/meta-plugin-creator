# Authoring commands

## Format

A command is a single markdown file at `commands/<command-name>.md` with
frontmatter:

```yaml
---
description: <one-line description shown in command lists>
---
```

The body is the prompt Claude Code runs when the user types
`/<plugin>:<command-name>`.

## Thin-wrapper pattern

A command carries no procedure of its own. Its entire body loads exactly one
skill and hands off to it — it does not duplicate the skill's steps, restate
its state protocol, or add extra logic. If two commands would need different
procedures, that is a sign they should load two different skills, not that
one command should grow its own logic.

Minimal command body:

```markdown
Load the `<skill-name>` skill and follow it, passing along any arguments the
user provided: $ARGUMENTS
```

## When to add a command at all

Add a command only when a skill needs an explicit, user-typed entry point
(typically because it is an explicit-invocation skill — see
`references/authoring-skills.md`). A model-invoked skill does not need a
command; Claude Code loads it from the description alone.
