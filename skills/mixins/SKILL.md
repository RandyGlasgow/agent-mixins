---
name: mixins
description: >
  Manage "mixins": small, single-purpose instruction files pulled into an
  agent's instructions file (CLAUDE.md, AGENTS.md, .github/copilot-
  instructions.md, ...) instead of pasted inline. Use when the user wants to
  add, remove, list, or audit a reusable instruction snippet, mentions
  "mixin", "mixins/...", or wants project/user instructions kept modular and
  easy to review one directive at a time. Works with any harness; uses
  native @path imports where the harness supports them and a marker-comment
  block otherwise.
license: MIT
---

# Mixins

A mixin is one instruction, in one file. Instead of a single instructions
file that grows a paragraph at a time until nobody remembers why a line is
there, each directive lives in its own file under
`mixins/<category>/<name>.md` and gets pulled into whatever file your
harness reads: `CLAUDE.md` for Claude Code, `AGENTS.md` for Codex and other
AGENTS.md readers, `.github/copilot-instructions.md` for Copilot, or
whatever the equivalent is.

How a mixin gets pulled in depends on whether the harness supports file
imports:

- **Claude Code** expands `@path` lines in CLAUDE.md, inlining the target
  file's contents wherever the line appears:
  ```
  @mixins/language/reading-level-8th-grade.md
  ```
  `@path` resolves relative to the file doing the importing. Recursion is
  limited to a few hops, and home-relative (`~/...`) imports have known bugs
  on some platforms, so keep mixins project-relative.

- **Everything else** (AGENTS.md, copilot-instructions.md, and any harness
  without a documented import syntax) has no way to reference an external
  file at read time, so the mixin's content is pasted directly into the
  instructions file, wrapped in a marker comment that names its source:
  ```
  <!-- mixin:language/reading-level-8th-grade.md -->
  Write for an 8th grade reading level.
  <!-- /mixin -->
  ```
  The marker keeps the one-directive-per-file property auditable even though
  the text is duplicated: you can still trace each block back to a single
  file, and adding or removing a mixin is still a matching add/delete instead
  of hand-editing prose in place.

Either way, one directive per file means you can read it, edit it, or delete
it without touching anything else.

## Layout

```
mixins/
  <category>/
    <name>.md
```

Category is a free-form grouping (`language/`, `style/`, `workflow/`, ...).
`name` is lowercase-hyphenated and names the single behavior the file
enforces. A mixin file has no frontmatter, just the instruction text, written
the way it should read once it lands in the instructions file.

This skill ships a starter catalog under `library/` (same shape, one level
up). Copy from there or write new ones directly.

## Create a mixin

1. Pick or make a category directory under `mixins/`.
2. Write the single directive to `mixins/<category>/<name>.md`. Keep it to a
   sentence or two, one behavior, no bundling multiple rules in one file.
3. Wire it into the instructions file where that directive belongs:
   - Claude Code: add `@mixins/<category>/<name>.md`.
   - Any other harness: paste the file's contents between
     `<!-- mixin:<category>/<name>.md -->` and `<!-- /mixin -->` markers.

## Apply a mixin from the library

Copy the file from this skill's `library/<category>/<name>.md` into the
target project's `mixins/<category>/<name>.md`, then wire it in as above.

## List mixins in a project

```bash
fd . mixins -e md
rg '^@mixins/|<!-- mixin:' CLAUDE.md AGENTS.md .github/copilot-instructions.md 2>/dev/null
```

## Clean up unwanted mixins

Removing a mixin is two deletes, not a search-and-scroll through a wall of
text:

1. Delete the wiring from the instructions file: the `@mixins/...` line, or
   the `<!-- mixin:... -->` through `<!-- /mixin -->` block.
2. Delete the file itself: `rm mixins/<category>/<name>.md`.

## Audit for drift

Cross-check the mixin files against what the instructions file references
and flag:

- **Orphaned imports/blocks**: an `@mixins/...` line or `<!-- mixin:...
  -->` block pointing at a file that no longer exists.
- **Orphaned files**: a file under `mixins/` that the instructions file no
  longer imports or embeds.

Either one means the mixin should be removed or re-wired, not left in place.
