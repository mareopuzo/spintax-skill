# spintax

A Claude Code skill that adds deliverability spintax to cold email copy **without changing meaning or tone**.

Spintax lets a cold email tool pick one option at random per send, so no two emails go out identical. That reduces spam-filter pattern detection and improves deliverability.

The skill supports two formats and **defaults to PlusVibe**:

| Format | Syntax | Use when |
|---|---|---|
| **PlusVibe** (default) | `{{random\|a\|b\|c}}` | Sending through PlusVibe |
| **Classic** (on request) | `{a\|b\|c}` | Sending through Smartlead / Instantly |

It only **adds** spintax — it does not pick between variations, rewrite your copy, or critique it. That keeps it predictable and safe to run on final copy.

## What it does

- Adds spintax to copy you paste (plain text **or** HTML), PlusVibe format by default.
- Defaults to 3 options per block.
- **Protects every merge token** — `{{first_name}}`, `{{First Name}}`, `{{Company}}`, any custom variable — plus `%signature%`, names, sign-offs, numbers, stats, and proof points. These are never spun.
- **Guarantees clean combinations**: every option the sender could randomly assemble reads as a natural, grammatical, complete sentence.
- **Never nests spin** (no `random` inside `random`); uses adjacent blocks and checks every cross-pairing.
- **Auto-fixes grammar** slips inside spin options, so no random pick ships an error.
- Preserves HTML structure exactly and returns copy-paste-ready output.

### PlusVibe specifics it enforces

- Only **one** variable per `{{random|...}}` option (two vars in one option breaks in PlusVibe).
- Spaces inside options are **literal**, so it keeps pipes tight (no stray spaces).
- Uses `{{fallback|{{first_name}}|there}}` to keep name-based greetings safe.
- Reminds you to run PlusVibe's **Preview Email + Test Email** before launch — PlusVibe fails a malformed spin silently.

### Dash handling (house style)

- Never introduces em dashes, en dashes, or new hyphens in spin options.
- Keeps your existing dashes as-is (separator lines, hyphenated words like `stress-test`).
- Flags any existing dashes once so you can decide whether to strip them.

## Install

The skill is a single Markdown file. Install it as a **global** skill:

```bash
mkdir -p ~/.claude/skills/spintax
cp skills/spintax/SKILL.md ~/.claude/skills/spintax/SKILL.md
```

Or **project-scoped**:

```bash
mkdir -p <your-project>/.claude/skills/spintax
cp skills/spintax/SKILL.md <your-project>/.claude/skills/spintax/SKILL.md
```

No build step, no dependencies.

## Use

In Claude Code, type `/spintax` then paste your email — or just say "spin this" / "add spintax". Works with plain text or HTML pasted straight from your sender.

**In:**

```
Hi {{First Name}},
Worth a quick look?
```

**Out (PlusVibe, default):**

```
{{random|Hi|Hello|Hey}} {{First Name}},
{{random|Worth a quick look?|Worth a look for your backlog?|Open to a quick look?}}
```

Ask for "classic spintax" for Smartlead / Instantly:

```
{Hi|Hello|Hey} {{First Name}},
{Worth a quick look?|Worth a look for your backlog?|Open to a quick look?}
```

## Notes

- Best used on **final** copy — spintax makes later editing harder, so spin last.
- PlusVibe spintax reference: https://help.plusvibe.ai/en/articles/8606174-spintax-guide
- The classic `{a|b|c}` syntax is identical across Smartlead and Instantly.
- `skills/spintax/SKILL.md` is the source of truth for the skill's behavior.
