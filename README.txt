===============================================================================
 SPINTAX — a Claude Code skill
===============================================================================

Adds deliverability spintax to cold email copy without changing meaning or tone.

Spintax is the syntax {option1|option2|option3}. Cold email tools like Smartlead
and Instantly pick one option at random per send, so no two emails go out
identical. That reduces spam-filter pattern detection and improves deliverability.

This skill only ADDS spintax. It does not pick between variations, rewrite your
copy, or critique it — that keeps it predictable and safe to run on final copy.


-------------------------------------------------------------------------------
 WHAT IT DOES
-------------------------------------------------------------------------------

- Adds {a|b|c} spintax to email copy you paste (plain text OR HTML).
- Defaults to 3 options per block.
- Protects every merge token — {{first_name}}, {{First Name}}, {{firstName}},
  {{Company}}, any custom variable, in any tool — plus %signature%, names,
  sign-offs, numbers, stats, and proof points. These are never spun.
- Guarantees clean combinations: every option the sender could randomly assemble
  reads as a natural, grammatical, complete sentence. No broken combos.
- Never nests spintax; uses adjacent blocks and checks every cross-pairing.
- Auto-fixes grammar slips inside spin options, so no random pick ships an error.
- Preserves your HTML structure exactly and returns copy-paste-ready output in a
  code block.

Dash handling (house style):
- Never introduces em dashes, en dashes, or new hyphens in spin options.
- Keeps your existing dashes as-is (your "-" separator lines, hyphenated words
  like "stress-test").
- Flags any existing dashes once so you can decide whether to strip them.

It also flags awkward copy briefly (without rewriting it), and for multi-email
sequences it works one email at a time, confirms "All combos clean.", then
prompts for the next.


-------------------------------------------------------------------------------
 INSTALL
-------------------------------------------------------------------------------

The skill is a single Markdown file. Install it as a GLOBAL skill (available in
every project on your machine):

    mkdir -p ~/.claude/skills/spintax
    cp skills/spintax/SKILL.md ~/.claude/skills/spintax/SKILL.md

Or install it PROJECT-SCOPED (only inside one project):

    mkdir -p <your-project>/.claude/skills/spintax
    cp skills/spintax/SKILL.md <your-project>/.claude/skills/spintax/SKILL.md

No build step, no dependencies. It is plain Markdown.


-------------------------------------------------------------------------------
 USE
-------------------------------------------------------------------------------

In Claude Code, type:

    /spintax

then paste your email. Or just say "spin this" / "add spintax" and paste the
copy. Works with plain text or with HTML pasted straight from Smartlead/Instantly.

Example in:

    Hi {{First Name}},
    Worth a quick look?

Example out:

    {Hi|Hello|Hey} {{First Name}},
    {Worth a quick look?|Worth a look for your backlog?|Open to a quick look?}


-------------------------------------------------------------------------------
 NOTES
-------------------------------------------------------------------------------

- Best used on FINAL copy. Spintax makes later editing and debugging harder, so
  spin last.
- The {a|b|c} syntax is identical across Smartlead and Instantly.
- The upstream "skills" CLI (npx skills ...) needs Node >= 22.20, but this skill
  needs no runtime of its own — it is just Markdown.

===============================================================================
