# agent-mixins

Mixins for your agents: small, single-purpose instruction files you pull into
CLAUDE.md by reference (Claude Code's `@path` import) instead of pasting
inline.

```
@.claude/mixins/language/reading-level-8th-grade.md
```

One file, one directive. Read it, change it, or delete it without touching
the rest of your instructions. Ships as a [skill](https://agentskills.io/)
for the [skills CLI](https://github.com/vercel-labs/skills).

[![skills.sh](https://skills.sh/b/RandyGlasgow/agent-mixins)](https://skills.sh/RandyGlasgow/agent-mixins)

## Install

Requires Node (for `npx`). No separate install step, `npx` downloads the
[skills CLI](https://github.com/vercel-labs/skills) on first run.

```bash
npx skills add RandyGlasgow/agent-mixins --skill mixins
```

This adds the `mixins` skill to Claude Code in the current project
(`.claude/skills/mixins/`). Answer the agent prompt to confirm, or add
`-y` to skip it.

**Install for every project instead of just this one:**

```bash
npx skills add RandyGlasgow/agent-mixins --skill mixins -g
```

**Install for a different agent** (Cursor, Codex, etc.), see the
[full agent list](https://github.com/vercel-labs/skills#supported-agents):

```bash
npx skills add RandyGlasgow/agent-mixins --skill mixins -a cursor
```

**Verify it installed:**

```bash
npx skills list
```

Installing the skill does not add any mixins to your project by itself,
it gives your agent the workflow for creating and applying them. Once
installed, ask your agent to apply a mixin from the starter library (for
example, "apply the reading-level-8th-grade mixin") or to create a new one.

## Available Skills

### mixins

Create, apply, list, and clean up `.claude/mixins/<category>/<name>.md`
files imported into CLAUDE.md via `@path` syntax. Includes a starter
library of common directives under `skills/mixins/library/`.

**Use when:**

- Adding a reusable instruction snippet to CLAUDE.md
- Keeping project or user instructions modular instead of one growing file
- Removing an instruction and making sure nothing still references it
- Auditing CLAUDE.md for imports pointing at deleted files
