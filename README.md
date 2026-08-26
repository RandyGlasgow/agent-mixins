# agent-mixins

Mixins for your agents: small, single-purpose instruction files you pull into
your agent's instructions file (CLAUDE.md, AGENTS.md, `.github/copilot-
instructions.md`, ...) instead of pasting inline.

```
@mixins/language/reading-level-8th-grade.md
```

One file, one directive. Read it, change it, or delete it without touching
the rest of your instructions. Ships as a [skill](https://agentskills.io/)
for the [skills CLI](https://github.com/vercel-labs/skills).

[![skills.sh](https://skills.sh/b/RandyGlasgow/agent-mixins)](https://skills.sh/RandyGlasgow/agent-mixins)

## Why mixins

Every coding agent reads an instructions file before it starts work.
Claude Code reads CLAUDE.md. Codex reads AGENTS.md. Copilot reads its own
instructions file. Teams fill these files with rules: how to write commit
messages, how to format code, what tone to use, when to ask before big
changes.

The trouble is these files grow one paragraph at a time. Someone adds a rule
during a rushed pull request. Someone else tweaks it six months later.
Nobody remembers why a line is there, and nobody wants to delete it in case
it breaks something. The file turns into a wall of text that is hard to read
and harder to trust.

A mixin cuts that wall of text into single, small files. Each file holds one
rule, and only one rule. Want to know why a team writes commit messages in
the imperative mood? Open one small file, not a five hundred line document.
Want to remove a rule that stopped helping? Delete one file and one line,
instead of hunting through prose to find where the rule starts and ends.

Teams also don't use one tool anymore. Some engineers run Claude Code, some
run Codex, some use Copilot. Each tool reads a different file, and only
Claude Code can pull in an outside file automatically with `@path`. The
others need the text pasted straight into their instructions file. Without a
shared system, a team ends up keeping the same rules written out separately
in CLAUDE.md, AGENTS.md, and the Copilot file, drifting apart every time
someone edits one and forgets the others.

The mixins skill keeps one canonical copy of each rule in a `mixins/`
folder. Claude Code links to it with `@path`. Every other tool gets the
same text copied in, wrapped in a marker comment that names the source
file. One source of truth, delivered however each tool needs it:

- Instructions stay small and legible instead of turning into a
  scroll-forever document.
- Removing a bad rule is a two-step delete, not surgery on a paragraph.
- The same rule reaches every tool a team uses, without three people
  maintaining three near-identical copies.
- A starter library ships with the skill, so common rules (write commit
  messages in the imperative mood, no em dashes, ask before large changes)
  are one copy-paste away instead of written from scratch every time.

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

Create, apply, list, and clean up `mixins/<category>/<name>.md` files.
Wires into CLAUDE.md via `@path` import for Claude Code, or into
AGENTS.md, `.github/copilot-instructions.md`, and other instructions files
via a marker-comment block for harnesses without an import syntax. Includes
a starter library of common directives under `skills/mixins/library/`.

**Use when:**

- Adding a reusable instruction snippet to your agent's instructions file
- Keeping project or user instructions modular instead of one growing file
- Removing an instruction and making sure nothing still references it
- Auditing an instructions file for imports pointing at deleted files
