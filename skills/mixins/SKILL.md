---
name: mixins
description: >
  Manage "mixins": small, single-purpose instruction files imported by
  reference into CLAUDE.md with Claude Code's @path syntax instead of pasted
  inline. Use when the user wants to add, remove, list, or audit a reusable
  instruction snippet in a CLAUDE.md file, mentions "mixin",
  "@.claude/mixins/...", or wants project/user instructions kept modular and
  easy to review one directive at a time.
license: MIT
---

# Mixins

A mixin is one instruction, in one file. Instead of a single CLAUDE.md that
grows a paragraph at a time until nobody remembers why a line is there, each
directive lives in its own file under `.claude/mixins/<category>/<name>.md`
and gets pulled into CLAUDE.md by reference:

```
@.claude/mixins/language/reading-level-8th-grade.md
```

Claude Code inlines the file's contents wherever the `@path` line appears.
One directive per file means you can read it, edit it, or delete it without
touching anything else.

This is a Claude Code CLAUDE.md feature. `@path` resolves relative to the
file doing the importing, which is why `@.claude/mixins/...` works when
CLAUDE.md sits at the project root. Other tools that read AGENTS.md do not
necessarily expand `@path` lines, so treat AGENTS.md support as
best-effort, not guaranteed. Recursion is limited to a few hops, and
home-relative (`~/...`) imports have known bugs on some platforms, so keep
mixins project-relative.

## Layout

```
.claude/mixins/
  <category>/
    <name>.md
```

Category is a free-form grouping (`language/`, `style/`, `workflow/`, ...).
`name` is lowercase-hyphenated and names the single behavior the file
enforces. A mixin file has no frontmatter, just the instruction text, written
the way it should read once inlined into CLAUDE.md.

This skill ships a starter catalog under `library/` (same shape, one level
up). Copy from there or write new ones directly.

## Create a mixin

1. Pick or make a category directory under `.claude/mixins/`.
2. Write the single directive to `.claude/mixins/<category>/<name>.md`. Keep
   it to a sentence or two, one behavior, no bundling multiple rules in one
   file.
3. Add the import line to CLAUDE.md where that instruction belongs:
   ```
   @.claude/mixins/<category>/<name>.md
   ```

## Apply a mixin from the library

Copy the file from this skill's `library/<category>/<name>.md` into the
target project's `.claude/mixins/<category>/<name>.md`, then add the same
import line as above.

## List mixins in a project

```bash
fd . .claude/mixins -e md
rg '^@\.claude/mixins/' CLAUDE.md
```

## Clean up unwanted mixins

Removing a mixin is two deletes, not a search-and-scroll through a wall of
text:

1. Delete the `@.claude/mixins/<category>/<name>.md` line from CLAUDE.md.
2. Delete the file itself: `rm .claude/mixins/<category>/<name>.md`.

## Audit for drift

Cross-check the two lists above and flag:

- **Orphaned imports**: an `@.claude/mixins/...` line in CLAUDE.md pointing
  at a file that no longer exists.
- **Orphaned files**: a file under `.claude/mixins/` that CLAUDE.md no
  longer imports.

Either one means the mixin should be removed or re-wired, not left in place.
