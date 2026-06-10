# CLAUDE.md

VC Design Standards — a static site of UX **decision records** for an enterprise
healthcare analytics product. Each record states what we do, why, and what the
major design systems do, so a contested choice gets settled by pointing at the
record rather than relitigating it in review.

Read `README.md` for the full procedure (structure, add-a-page workflow, status
lifecycle, GitHub Pages). This file is the working contract: what not to break,
how a record is shaped, and the voice the prose is written in.

## The stack is the constraint — do not "improve" it

Plain HTML + CSS. No JavaScript, no build step, no framework, no dependencies.
This was chosen deliberately over Astro/Next: the site is ~10–20 pages optimised
for durability and zero maintenance, and it must work opened straight from disk
(`index.html`) as well as on GitHub Pages. Treat these as hard rules:

- **No `package.json`, no bundler, no generator, no npm.** If you reach for
  tooling, you have misread the project.
- **No JavaScript.** The only exception is a small inline script scoped to one
  demo that genuinely needs it to show an interaction. There are none so far;
  adding one is a decision to flag, not a default.
- **No inline `<style>` blocks.** Pages link `css/tokens.css` and `css/base.css`.
  A single demo may carry scoped styles only if it genuinely needs them.
- **Relative paths everywhere** (`../css/base.css`, never `/css/base.css`).
  Absolute paths break on GitHub Pages project URLs. The few inline `style=""`
  attributes on the masthead are existing spacing tweaks — match that pattern,
  don't migrate them out.

## The visual language is canonical — do not redesign it

OKLCH paper/ink palette, Source Serif 4 / Hanken Grotesk / IBM Plex Mono (Google
Fonts), 64ch measure. It lives in `css/tokens.css` (`:root` custom properties —
`--paper*`, `--ink*`, `--rule*`, `--warm`/`--cool` brand accents, `--measure`,
`--gutter`) and `css/base.css`. Reuse tokens; never hard-code a colour or a
measure. Adding a record changes content, not CSS — if a record seems to need a
new component style, that is a conversation, not a quiet addition.

## How a record is built

Adding a record = duplicate `records/_template.html`, fill every `<!-- TODO -->`,
add one `<a class="dircard">` to `index.html` under the right category section.
That is the entire workflow — no registration, nothing to generate.

Every record shares one shell and this anatomy:

- **`.topnav`** — brand linking to the index + a category eyebrow.
- **`.masthead`** — `.rechead` (status badge + category eyebrow), `<h1>`,
  `.lead` summary, then two framed blocks: **`.context`** ("Our context" — the
  product reality that decides the question for VC) and **`.rulecard`** ("The
  rule" — the house rule, plainly stated; mark it "(provisional)" while Draft).
- **Numbered `<section>`s** using the `.secnum` pattern (`01 / Quick reference`,
  `02 / The pattern in practice`, …). Body copy stays inside `.prose` to hold
  the measure; tables and demos may break wider.
- **Good/bad comparisons** use `.compare` with `.stage.avoid` / `.stage.use`
  (labelled **Avoid** / **Use**): side by side on desktop, stacked on narrow
  viewports. Reuse `.tbl`, `.principles`, `.systems`/`.syscard`, `.reco` from
  the stepper record rather than inventing new structures.
- **`.globalfoot`** — the shared footer, verbatim.

A record's status badge appears in two places — the `.rechead` masthead and its
`index.html` dircard. **Keep them in sync.** Status set: `draft` (argue with it,
don't cite it) → `adopted` (the house rule, cite it in review) → `superseded`
(replaced; link the replacement).

## Content scope

Pattern-level only: layout rules, labelling rules, generic demos. **No
product-specific screens, data, PHI, or real patient/member content.** Demos use
the neutral `.appframe` chrome with placeholder labels. Precedent sections cite
named design systems (Carbon, Material, Polaris, Atlassian, etc.); keep real
cited quotes accurate and sourced in a `References` section. A precedent section
left as a "To be completed" stub is intentional — do not fill it in to look
finished.

**This site is public-facing — protect the product and its IP.** Always write
**"VC"**, never the full product name (no "Value Connect") and never the parent
org. Do not expose proprietary capability mechanics: the natural-language
registry feature is named **"AI filters"** in all copy — never "Text-to-SQL",
and never describe its internals (prompt → generated/validated SQL → preview).
Name the capability, not how it works.

## The voice — match it exactly

The prose is the product. Study `records/stepper-orientation.html` before
writing; new copy should be indistinguishable in register.

- **Declarative and settled. No hedging.** "We ship both orientations. This sets
  out which to reach for, and why." Not "you might want to consider…".
- **Anchored to VC's reality**, never abstract best-practice. Every rule earns
  itself by naming the users (analysts, care managers, config admins on large
  desktop monitors) and the work (long, server-backed configuration: cohort and
  registry builders, quality-measure definition, risk and attribution setup).
- **State the rule, then the deciding question.** "Default to X. Reserve Y for…
  The deciding question is…"
- **Indian/British spelling** — organise, colour, prioritise, behaviour.
- **Write to be scanned, not read.** The audience skims. Lead every block with
  its claim; the full standard is below.
- **No clinical disclaimers, no "as an AI", no filler.** Insight per sentence.

### Write to be scanned

Readers skim, not read — including teammates opening a record for the first
time. The point of every block must land from its first line. `stepper-orientation.html`
and `modal-vs-page.html` §03 are the reference for this register.

- **Lead with the claim.** Open each paragraph, principle, section intro and demo
  `.note` with its load-bearing assertion as a short **bolded** sentence. The
  decision should be gettable from that first line alone.
- **One idea per block.** 1–3 short sentences, never a wall. Cut throat-clearing
  ("The first decision is…", "by nature") and reasoning the numbered sections
  already carry.
- **Lists for parallel sets.** Any enumeration — failure modes, conditions,
  options — becomes a `ul.clean` list (use the `.cool`/`.anti` variants where the
  colour carries meaning), not a comma-spliced sentence. Place the `<ul>` as a
  block sibling, **never inside a `<p>`** (`.principle p` / `.context p` are styled).
- **Principles are headline + one line.** In a `.principles` grid the `<h3>`
  carries the claim; the body is one short sentence or a 2–3 item list, not a
  paragraph.
- **Numerals and the bare directive.** "Past 4 fields → full page", not "past
  about four fields … belongs on a full page or in a side panel." Use `→`
  sparingly for "promote to".

## Before you finish

Open `index.html` from disk and click through: links resolve relatively, fonts
and palette render, new record uses the identical shell, badges match between
masthead and dircard, good/bad demos stack on a narrow viewport. There are no
tests and no CI — visual inspection from disk is the check.
