---
description: Two-pass copy improvement (scannable, then humanise wiring) for one record page
---

Improve the copy on exactly ONE page: $ARGUMENTS. Do not touch any other file.

## Read first (do not skip)
1. The "voice" and "Write to be scanned" sections of CLAUDE.md.
2. records/stepper-orientation.html — this is the target register. Match it.
3. The target page in full, before changing anything.

## What "humanise" means HERE (keep it narrow)
This site's voice is declarative, settled, and scannable. "Humanise" does NOT
mean warmer, chattier, or more conversational. It means:
- Cut machine tells: templated parallelism, throat-clearing ("The first
  decision is…", "It is important to note…"), and transition-only sentences.
- Vary sentence rhythm — never five same-length sentences in a row. Mix a short
  punch with a longer line.
- Make the wiring causal ("X. So Y." / "Reserve Y for…"), not additive
  ("Additionally, furthermore, moreover").
- Stay ruthlessly settled. Humanising NEVER adds hedging ("you might",
  "in some cases", "consider", "perhaps"). No qualifier survives this pass.

## Pass A — Scannability (do this first)
- Each paragraph / principle / section intro / demo .note opens with a short
  BOLDED claim that is gettable on its own.
- Comma-spliced enumerations become ul.clean lists, placed as block siblings,
  never inside a <p>. Use the .cool / .anti variants where colour carries meaning.
- Principles = <h3> claim + one sentence or a 2–3 item list. No paragraphs.
- Numerals and bare directives ("Past 4 fields → full page"). Use → sparingly
  for "promote to".

## Pass B — Humanise the wiring (only after A lands)
- Rewrite transitions and rhythm per the definition above.
- Anchor every rule to VC's reality — analysts, care managers, config admins on
  large desktop monitors; long, server-backed configuration (cohort and registry
  builders, quality-measure definition, risk and attribution setup). Never
  abstract best-practice.
- Indian/British spelling: organise, colour, behaviour, prioritise.

## Never touch (hard stops)
- No HTML structure, class, or CSS changes. Edit copy inside existing elements only.
- No new components, no inline <style>, no JavaScript.
- Keep all paths relative (../css/base.css). Don't change status badges, and keep
  the masthead badge in sync with the index.html dircard.
- Don't invent facts, numbers, or precedent. Don't fill a "To be completed" stub.
- Keep cited quotes in any References section verbatim and accurate.
- Always "VC", never the full product name or the parent org.
- The registry feature is "AI filters" — never "Text-to-SQL", never its internals.
- No PHI, no real patient/member content.

## Finish
- Summarise what changed and WHY — one line per change.
- Do NOT commit. Stop and wait for review against stepper-orientation.html.
