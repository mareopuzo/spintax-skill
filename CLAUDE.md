# CLAUDE.md

Guidance for Claude Code when working in this repository.

## What this repo is

This repo packages a single Claude Code skill: **`spintax`**. The skill adds
deliverability spintax to cold email copy without changing meaning or tone. It is
spintax-only — it does not pick between variations, rewrite copy, or critique it.

The skill supports two output formats and **defaults to PlusVibe**:

- **PlusVibe (default):** `{{random|option1|option2|option3}}` — double braces,
  leading `random`. Fallback for empty merge vars: `{{fallback|{{var}}|default}}`.
- **Classic (on request):** `{option1|option2|option3}` — single braces, for
  Smartlead / Instantly.

The authoritative definition of the skill's behavior is
[`skills/spintax/SKILL.md`](skills/spintax/SKILL.md). If you change how the skill
behaves, edit that file — it is the source of truth, not this file.

## Repo structure

```
spintax-skill/
├── CLAUDE.md                 # this file — guidance for Claude Code
├── README.md                 # human-facing docs (GitHub homepage)
├── README.txt                # plain-text mirror of the docs
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
- **No nested spin.** Never a spin inside a spin. In PlusVibe that means no
  `random` inside `random`; the only thing allowed inside a `random` option is a
  single merge variable or a `fallback`. Split into two adjacent blocks and verify
  every cross-pairing.
- **PlusVibe specifics:** only ONE variable per `random` option; spaces inside
  options are literal (keep pipes tight, no stray spaces); write `random` /
  `fallback` lowercase; remind the user to run Preview Email + Test Email before
  launch (PlusVibe fails a malformed spin silently).
- **Never spin:** any merge token (`{{first_name}}`, `{{Company}}`, any tool),
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
