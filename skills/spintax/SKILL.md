---
name: spintax
description: Adds deliverability spintax to cold email copy without changing meaning or tone. Use whenever the user asks to add spintax, spin text, add variations, or improve deliverability of email copy — especially for Smartlead or Instantly cold email sequences, or when they paste email HTML. Trigger on "spintax", "spin", "spin this", "add variations", "deliverability spintax". Works with plain text and HTML.
---

# Spintax

Add deliverability-improving spintax to cold email copy using `{option1|option2|option3}` syntax, without changing the meaning or tone of the original. This skill only adds spintax — it does not pick between variations, rewrite copy, or critique it unless the user explicitly asks for that separately.

## Core concept

Spintax lets the sending tool (Smartlead, Instantly, etc.) randomly pick one option per send, so no two emails are identical. This reduces pattern detection by spam filters and improves deliverability.

**Syntax:** `{option1|option2|option3}` — options separated by a pipe `|`, one picked at random per send. This syntax is the same across Smartlead and Instantly.

---

## Input handling

### HTML input (preferred)

Users often paste HTML from their sending tool because copy-pasting plain text loses formatting (spaces, line breaks). When given HTML:

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

**BAD — dependent blocks that can break:**
```
{let me know if|would} {{first_name}} {would be better to speak to|be a better person to chat with}
```
"would" + "would be better to speak to" = "would {{first_name}} would be better to speak to" — broken.

**GOOD — each option is a full standalone phrase:**
```
{let me know if {{first_name}} would be better to speak to about this?|would {{first_name}} be a better person to chat with about this?|should I be reaching out to {{first_name}} about this instead?}
```

### Adjacent blocks

Two spintax blocks next to each other are fine **only if every cross-pairing reads clean**. Walk through the full grid before shipping. Example that works because all six pairings are grammatical:
```
{With {{Company}} scaling its healthcare|As {{Company}} scales its healthcare|Now that {{Company}} is scaling its healthcare} {platform|software}, ...
```

### Never nest spintax

Do not put a spintax block inside another block: `{... {a|b} ...}`. Nested spintax is unreliable across tools. If a phrase you want to spin already contains a spin block, split it into two adjacent blocks instead and verify the cross-pairings (see above).

### Verification step

After adding spintax, mentally walk every combination across adjacent blocks. If any pairing sounds off, restructure so each block is independent. When independent blocks are too risky, wrap the whole sentence in one block with full-sentence alternatives.

---

## What to spin

Default to **3 options per block**. Target:

### Always spin
- **Greetings:** `{Hey|Hi|Hello}`
- **Opt-out / breakup / close-the-loop lines:** repetitive across a sequence, easy to spin without tone change
- **CTAs:** different phrasings of the same ask
- **Transition words and connectors:** "just", "also", "actually", etc.

### Spin when natural
- **Verb choices:** `{help|work with|partner with}`, `{deploying|shipping|pushing}`, `{handles|owns|runs}`
- **Descriptors:** `{at no cost|completely free|on us}`
- **Sentence-level rephrasings:** when a line can be said 2-3 ways without changing meaning. Each option must be a complete, standalone sentence.

### Never spin
- **Any `{{...}}` variable token** — `{{first_name}}`, `{{First Name}}`, `{{firstName}}`, `{{Company}}`, `{{company_name}}`, any custom merge field, in any tool. Leave exactly as-is.
- **Signature placeholders:** `%signature%` — never touch
- **Signatures, names, sign-offs, and separators** (e.g. a `-` line above the name) — leave as-is
- **Specific data points:** numbers, stats, brand/product names, pricing, proof points (e.g. a customer name like SutureHealth)
- **Precise technical terms** that need to stay exact

---

## Tone preservation

Spin options must match the tone and register of the original. Casual stays casual; direct stays direct. Don't add formality where there was none, don't add slang where it was professional.

- Casual/direct original: `{Worth a shot?|Want to give it a try?|Open to trying it out?}` ✅
- Register mismatch: `{Would you be amenable to a trial?|Worth a shot?}` ❌

---

## Baked-in behaviors

### Auto-fix grammar in spins
If the source copy has a grammar slip (missing subject, broken parallelism, subject-verb disagreement), make **every** spin option grammatical so no random pick ships the error. Note the fix briefly after the output. Example: source "…halts clinical workflows and tank customer trust" → every option uses "tanks" / "erodes".

### Dash handling
- **Never introduce** em dashes, en dashes, or new hyphens in any spin option. The user reads dashes as an AI tell in visible copy. Rephrase instead.
- **Keep the user's existing dashes as-is** — their `-` separator lines and hyphenated words (e.g. "stress-test", "high-compliance") stay untouched.
- **Flag existing dashes** once, briefly, after the output so the user can decide whether to strip them. Don't remove them yourself.

### Flag awkward copy
If something in the original is awkward or would hurt performance (independent of spintax), flag it briefly after the output — one line, e.g. "One flag: 'code to {{Company}} build' is missing an article; consider 'the {{Company}} build'." Keep flags minimal. Do not rewrite their copy unless asked.

---

## Output format

Return the spintaxed copy in a code block — `html` fence for HTML input, plain fence for plain text.

**HTML:**
```html
{Hey|Hi|Hello} {{first_name}}, {open to a free backlink on|interested in a free backlink from|want a free backlink on} Forbes, WSJ, or Tech Times for {{company_name}}?
```

**Plain text:**
```
{Hey|Hi|Hello} {{first_name}}, {open to a free backlink on|interested in a free backlink from|want a free backlink on} Forbes, WSJ, or Tech Times for {{company_name}}?
```

### After each email
State **"All combos clean."** — confirming you verified every combination. If you fixed a grammar slip, note what changed and why. Add any copy flag and any dash flag on their own lines. Then prompt: **"Next? Drop the next one."**

---

## Multi-email sequences

Process one email at a time. Keep a mental count of which email you're on (Email 1, Email 2, …) so you can reference them. After each: output the spintaxed version, confirm combos are clean, then prompt for the next.

---

## Quick reference

| Element | Example |
|---|---|
| Syntax | `{option1\|option2\|option3}` |
| Greeting | `{Hey\|Hi\|Hello}` |
| Verb swap | `{help\|work with\|partner with}` |
| Full sentence | `{Is this worth exploring?\|Want to give it a try?\|Open to a quick chat?}` |
| Opt-out | `{Not relevant? Just reply and I won't reach out again.\|If this isn't a fit, reply and I'll close the loop.}` |
| Never touch | any `{{...}}` token, `%signature%`, names, stats, `-` separators |
