---
name: spintax
description: Adds deliverability spintax to cold email copy without changing meaning or tone. Defaults to PlusVibe spintax syntax; also supports classic Smartlead/Instantly syntax on request. Use whenever the user asks to add spintax, spin text, add variations, or improve deliverability of email copy — especially for PlusVibe, Smartlead, or Instantly cold email sequences, or when they paste email HTML. Trigger on "spintax", "spin", "spin this", "add variations", "deliverability spintax", "plusvibe". Works with plain text and HTML.
---

# Spintax

Add deliverability-improving spintax to cold email copy without changing the meaning or tone of the original. This skill only adds spintax — it does not pick between variations, rewrite copy, or critique it unless the user explicitly asks for that separately.

## Core concept

Spintax lets the sending tool randomly pick one option per send, so no two emails are identical. This reduces pattern detection by spam filters and improves deliverability.

There are two output formats. **Default to PlusVibe format**, since that is the sequencer in use. Only emit classic format if the user asks for Smartlead/Instantly/classic spintax.

| Format | Syntax | Use when |
|---|---|---|
| **PlusVibe (default)** | `{{random\|option1\|option2\|option3}}` | Sending through PlusVibe |
| **Classic** | `{option1\|option2\|option3}` | Sending through Smartlead, Instantly, or when the user asks for classic |

Ask which sequencer only if it is genuinely ambiguous and the user hasn't said. Otherwise assume PlusVibe.

---

## PlusVibe format (default)

PlusVibe uses a keyword-function syntax inside **double** braces, and it uses the same double braces for merge variables. Two functions matter:

### 1. Random (the spin function)

```
{{random|option1|option2|option3}}
```

- Options separated by pipes `|`; PlusVibe picks one at random per send.
- Example: `{{random|Hello|Hey|Hi}}`
- `random` is case-insensitive (`RANDOM`, `random`, `rAnDoM` all work). Always write it lowercase: `random`.
- **Spaces inside options are preserved exactly.** `{{random|hello| there}}` would ship a literal leading space. Never leave a stray space right after a `|` or right before the closing `}}` unless you intend it. Trim option edges.
- An empty option is allowed with `||`: `{{random||Hello}}` ships either nothing or "Hello". Use this deliberately, never by accident.

### 2. Fallback (default value for a merge variable)

```
{{fallback|{{variable}}|default_text}}
```

- Ships the variable's value, or the default text when the variable is missing or empty.
- Example: `{{fallback|{{first_name}}|there}}`
- `fallback` is case-insensitive; spaces are preserved the same way.
- Use this to make greetings safe when a first name may be blank. Prefer a natural default that fits the tone (`there`, not `Sir`).

### Merge variables

Merge tags are plain double braces: `{{first_name}}`, `{{company_name}}`, any custom field. Leave every merge tag exactly as received. **Never wrap a bare merge tag in `random`** and never spin the tag itself.

### PlusVibe-specific rules (critical)

1. **Only ONE variable per random section.** A variable may live inside a random option, but only one.
   - Works: `{{random|Hello {{first_name}}|Hi there}}`
   - Breaks: `{{random|Hello {{first_name}} {{last_name}}|Hi there}}`
   - If an option needs two variables, don't spin that option's variables — pull the sentence apart so each `random` block carries at most one, or leave that phrase unspun.
2. **Do not nest `random` inside `random`.** The only nesting PlusVibe supports is a single merge variable (or a `fallback`) inside a `random` option — not a spin inside a spin. If you want to spin two things in one sentence, use two adjacent `random` blocks and verify every cross-pairing (see the golden rule).
3. **Watch the pipes and spaces.** Because spaces are literal, keep pipes tight: `{{random|help|work with|partner with}}`, never `{{random| help | work with }}`.
4. **Always end reminders with Preview + Test.** PlusVibe fails a malformed spin silently, so tell the user to run "Preview Email" and "Test Email" before launching. State this once per session, not per email.

---

## Classic format (on request)

`{option1|option2|option3}` — single braces, pipe-separated, one picked at random. Identical across Smartlead and Instantly. All the golden-rule and "what to spin" guidance below applies unchanged; only the wrapper differs (`{...}` instead of `{{random|...}}`). In classic format, merge tags `{{...}}` are protected and never spun.

---

## Input handling

### HTML input (preferred)

Users often paste HTML from their sending tool because plain text loses formatting. When given HTML:

1. Parse the text content from the HTML.
2. Add spintax to the text content only.
3. Return the full HTML with spintax baked in, preserving every tag, `<br>`, `<div>`, and structure exactly as received.
4. Output as a code block so the user can paste straight back in.

### Plain text input

Add spintax and return plain text in a code block. Preserve the user's line breaks exactly (including consecutive lines with no blank line between them, e.g. greeting + first line).

---

## The golden rule: no broken combinations

Every possible combination the tool could randomly assemble MUST read as a natural, grammatically correct, complete sentence. This is the single most important rule.

**Make each spintax block a self-contained phrase.** The options within one block should be interchangeable regardless of what a nearby block picks.

**BAD — dependent blocks that can break (shown in PlusVibe format):**
```
{{random|let me know if|would}} {{first_name}} {{random|would be better to speak to|be a better person to chat with}}
```
"would" + "would be better to speak to" = "would {{first_name}} would be better to speak to" — broken.

**GOOD — each option is a full standalone phrase:**
```
{{random|let me know if {{first_name}} would be better to speak to about this?|would {{first_name}} be a better person to chat with about this?|should I be reaching out to {{first_name}} about this instead?}}
```
(Each option carries at most one variable, satisfying the PlusVibe one-variable rule.)

### Adjacent blocks

Two spintax blocks next to each other are fine **only if every cross-pairing reads clean**. Walk through the full grid before shipping. Example that works because all six pairings are grammatical:
```
{{random|With {{Company}} scaling its healthcare|As {{Company}} scales its healthcare|Now that {{Company}} is scaling its healthcare}} {{random|platform|software}}, ...
```

### Never nest spintax

Do not put a spin block inside another spin block. In PlusVibe this means no `random` inside `random`. If a phrase you want to spin already contains a spin block, split it into two adjacent blocks instead and verify the cross-pairings (see above). The only thing allowed inside a `random` option is a single merge variable or a `fallback`.

### Verification step

After adding spintax, mentally walk every combination across adjacent blocks. If any pairing sounds off, restructure so each block is independent. When independent blocks are too risky, wrap the whole sentence in one block with full-sentence alternatives. For PlusVibe, also confirm each `random` option holds at most one variable and no nested `random`.

---

## What to spin

Default to **3 options per block**. Target:

### Always spin
- **Greetings:** `{{random|Hey|Hi|Hello}}`
- **Opt-out / breakup / close-the-loop lines:** repetitive across a sequence, easy to spin without tone change
- **CTAs:** different phrasings of the same ask
- **Transition words and connectors:** "just", "also", "actually", etc.

### Spin when natural
- **Verb choices:** `{{random|help|work with|partner with}}`, `{{random|deploying|shipping|pushing}}`, `{{random|handles|owns|runs}}`
- **Descriptors:** `{{random|at no cost|completely free|on us}}`
- **Sentence-level rephrasings:** when a line can be said 2-3 ways without changing meaning. Each option must be a complete, standalone sentence.

### Never spin
- **Any merge variable token** — `{{first_name}}`, `{{First Name}}`, `{{firstName}}`, `{{Company}}`, `{{company_name}}`, any custom merge field. Leave exactly as-is. Never wrap one in `random`.
- **Signature placeholders:** `%signature%` — never touch
- **Signatures, names, sign-offs, and separators** — leave as-is
- **Specific data points:** numbers, stats, brand/product names, pricing, proof points (e.g. a customer name like SutureHealth)
- **Precise technical terms** that need to stay exact

---

## Tone preservation

Spin options must match the tone and register of the original. Casual stays casual; direct stays direct. Don't add formality where there was none, don't add slang where it was professional.

- Casual/direct original: `{{random|Worth a shot?|Want to give it a try?|Open to trying it out?}}` ✅
- Register mismatch: `{{random|Would you be amenable to a trial?|Worth a shot?}}` ❌

---

## Baked-in behaviors

### Auto-fix grammar in spins
If the source copy has a grammar slip (missing subject, broken parallelism, subject-verb disagreement), make **every** spin option grammatical so no random pick ships the error. Note the fix briefly after the output. Example: source "…halts clinical workflows and tank customer trust" → every option uses "tanks" / "erodes".

### Dash handling
- **Never introduce** em dashes, en dashes, or new hyphens in any spin option. The user reads dashes as an AI tell in visible copy. Rephrase instead.
- **Keep the user's existing dashes as-is** — their `-` separator lines and hyphenated words (e.g. "stress-test", "high-compliance") stay untouched.
- **Flag existing dashes** once, briefly, after the output so the user can decide whether to strip them. Don't remove them yourself.

### Flag awkward copy
If something in the original is awkward or would hurt performance (independent of spintax), flag it briefly after the output — one line. Keep flags minimal. Do not rewrite their copy unless asked.

---

## Output format

Return the spintaxed copy in a code block — `html` fence for HTML input, plain fence for plain text.

**PlusVibe, plain text:**
```
{{random|Hey|Hi|Hello}} {{first_name}}, {{random|open to a free backlink on|interested in a free backlink from|want a free backlink on}} Forbes, WSJ, or Tech Times for {{company_name}}?
```

**PlusVibe, HTML:**
```html
{{random|Hey|Hi|Hello}} {{first_name}}, {{random|open to a free backlink on|interested in a free backlink from|want a free backlink on}} Forbes, WSJ, or Tech Times for {{company_name}}?
```

### After each email
State **"All combos clean."** — confirming you verified every combination (and, for PlusVibe, that every `random` option holds at most one variable with no nested `random`). If you fixed a grammar slip, note what changed and why. Add any copy flag and any dash flag on their own lines. Then prompt: **"Next? Drop the next one."**

Remind the user once per session (not per email) to run PlusVibe's **Preview Email** and **Test Email** before launching.

---

## Multi-email sequences

Process one email at a time. Keep a mental count of which email you're on (Email 1, Email 2, …) so you can reference them. After each: output the spintaxed version, confirm combos are clean, then prompt for the next.

---

## Quick reference

| Element | PlusVibe (default) | Classic |
|---|---|---|
| Spin syntax | `{{random\|a\|b\|c}}` | `{a\|b\|c}` |
| Greeting | `{{random\|Hey\|Hi\|Hello}}` | `{Hey\|Hi\|Hello}` |
| Verb swap | `{{random\|help\|work with\|partner with}}` | `{help\|work with\|partner with}` |
| Full sentence | `{{random\|Is this worth exploring?\|Want to give it a try?\|Open to a quick chat?}}` | `{Is this worth exploring?\|Want to give it a try?\|Open to a quick chat?}` |
| Name fallback | `{{fallback\|{{first_name}}\|there}}` | (n/a — tool-specific) |
| Never touch | merge tags `{{...}}`, `%signature%`, names, stats, `-` separators | same |

### PlusVibe pitfalls checklist
- [ ] No stray spaces just inside `|` or before `}}` (spaces are literal)
- [ ] Each `random` option carries at most ONE merge variable
- [ ] No `random` nested inside a `random`
- [ ] `random` / `fallback` written lowercase
- [ ] User reminded to Preview + Test before sending
