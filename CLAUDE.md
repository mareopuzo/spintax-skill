# CLAUDE.md

Guidance for Claude Code when working in this repository.

## What this repo is

This repo packages a single Claude Code skill: **`spintax`**. The skill adds
deliverability spintax (`{option1|option2|option3}`) to cold email copy without
changing meaning or tone. It is spintax-only — it does not pick between
variations, rewrite copy, or critique it.

The authoritative definition of the skill's behavior is
[`skills/spintax/SKILL.md`](skills/spintax/SKILL.md). If you change how the skill
behaves, edit that file — it is the source of truth, not this file.

## Repo structure

```
spintax-skill/
├── CLAUDE.md                 # this file — guidance for Claude Code
├── README.txt                # human-facing install + usage docs
└── skills/
    └── spintax/
        └── SKILL.md          # the skill definition (frontmatter + instructions)
```

## Installing the skill locally

The skill is a plain Markdown file. To install it as a global skill:

```
mkdir -p ~/.claude/skills/spintax
cp skills/spintax/SKILL.md ~/.claude/skills/spintax/SKILL.md
```

It then appears as `/spintax` in every project. A project-scoped install goes to
`<project>/.claude/skills/spintax/SKILL.md` instead.

## Core rules the skill enforces (summary — SKILL.md is authoritative)

- **Golden rule:** every combination the sender could randomly assemble must read
  as a natural, grammatical, complete sentence. Make each block a self-contained
  phrase.
- **No nested spintax.** Never `{... {a|b} ...}`. Split into two adjacent blocks
  and verify every cross-pairing.
- **Never spin:** any `{{...}}` merge token (Smartlead, Instantly, any tool),
  `%signature%`, names, sign-offs, `-` separators, numbers, stats, or proof points.
- **3 options per block** by default.
- **Auto-fix grammar** inside spin options so no random pick ships an error.
- **Dashes:** never introduce em/en dashes or new hyphens in spin options; keep
  the user's existing dashes as-is; flag existing dashes once so they can decide.
- **Flag awkward copy** briefly without rewriting it.
- **Sequence flow:** one email at a time; confirm "All combos clean."; prompt for
  the next.

## Editing conventions

- Keep the YAML frontmatter valid: `name` and `description` are required, both on
  a single line. Avoid a bare `: ` inside the `description` value — it breaks the
  YAML parser some skill loaders use.
- Keep the skill spintax-only. Variation-picking and copy critique are
  deliberately out of scope; do not add them without an explicit request.

## Tooling note

The upstream `skills` CLI (`npx skills ...`) requires Node >= 22.20. If a machine's
default `node` is older, run it under a newer Node explicitly. This skill itself is
plain Markdown and needs no runtime.
