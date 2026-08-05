# Designing a plugin's own state filesystem

This reference is for designing the state layout of the *plugin being
produced* — not this `plugin-dev` skill's own state file (that protocol is
fixed in `SKILL.md`).

## Externalize cross-step state (C-002)

Any behavior in the produced plugin that spans more than one step, or more
than one session, must keep its state in a declared file, not only in
conversation. A user (or a lower-tier model) must be able to inspect,
resume, or verify progress by reading a file, without replaying the
conversation.

## Default state directory

Name the state directory after the plugin: `.{plugin-name}/`. Ask during the
interview (question 4) whether this default works or a different name is
needed — do not invent a name unprompted.

Typical contents: a single markdown file (e.g. `plan.md` or `state.md`)
holding whatever the plugin's steps need to persist — a purpose statement,
decisions, a status table, a dated log. Match the shape to what the produced
plugin actually needs; do not copy `plugin-dev`'s own plan-file schema by
default if the target plugin's state needs differ.

## Read-at-step-start / write-at-step-end protocol

Every step in the produced plugin's skill(s) should:

1. Read the state file before doing any work, to pick up prior decisions and
   status.
2. Do the step's work.
3. Write the state file before the step ends — updated status, new
   decisions, a dated log line.

## Resume from state file

A skill should always check its state file's default location first when
starting. If found, resume from its content instead of re-asking questions
already answered. If absent, treat the session as new and, if the skill has
an interview, run it.
