# AGENTS.md

VC Design Standards is a static site of UX **decision records** for an enterprise healthcare analytics product. Records settle contested design choices: state what VC does, why, and what major design systems do. Read `README.md` for the complete procedure, structure, status lifecycle, and GitHub Pages deployment details.

## Non-negotiable stack constraints

- Use plain HTML and CSS only. No JavaScript, build step, framework, dependencies, `package.json`, bundler, generator, or npm.
- Use relative paths everywhere, such as `../css/base.css`; GitHub Pages project URLs break absolute paths.
- Do not add inline `<style>` blocks. The only exception is a demo that cannot use shared CSS: scope it in that record's `<head>`, namespace its classes, use tokens only, and flag it. `records/breakpoints.html` §03 is the precedent.
- Existing inline masthead `style=""` attributes are spacing tweaks; match that pattern rather than migrating them.

## Visual language

The design system is canonical; do not redesign it. Reuse the OKLCH paper/ink palette, Source Serif 4, Hanken Grotesk, IBM Plex Mono, and 64ch measure defined in `css/tokens.css` and `css/base.css`. Use existing tokens (`--paper*`, `--ink*`, `--rule*`, `--warm`, `--cool`, `--measure`, `--gutter`) rather than hard-coded colours or measures. New records change content, not CSS; discuss any need for a new component style.

## Adding or editing records

- For a new record, duplicate `records/_template.html`, complete every `<!-- TODO -->`, add a `.regrow` link to the appropriate `index.html` `.reggroup` or `.regsub`, and update that category's `.homenav` count.
- Preserve the shared shell: `.topnav`, `.masthead`, numbered `.secnum` sections, and the verbatim `.globalfoot`.
- The masthead contains `.rechead`, `<h1>`, `.lead`, `.context` (**Our context**), and `.rulecard` (**The rule**). Mark Draft rules as `(provisional)`.
- Keep status badges in sync between the record masthead and its index `.regrow`: `draft` → `adopted` → `superseded`. Drafts are argued with, adopted records are cited, and superseded records link to their replacement.
- Put body copy in `.prose`. Reuse established primitives, especially `.appframe`, `.compare`, `.stage.avoid`, `.stage.use`, `.tbl`, `.principles`, `.systems` / `.syscard`, and `.reco`.

## Demonstration and content boundaries

- Every rule needs a working, schematic demo. Make the behaviour visible with shared primitives; `records/breakpoints.html` §02–03 is the reference.
- Teach with contrast: place **Avoid** and **Use** beside each other in a `.compare` block. Demos are neutral, token-driven schematics—not product mock-ups.
- Stay pattern-level: generic layout and labelling rules only. Never include product-specific screens, data, PHI, or real patient/member content.
- Cite named design systems accurately in a `References` section. Preserve intentional “To be completed” precedent stubs.
- This site is public-facing. Write **VC**, never the full product or parent organisation. Refer to the natural-language registry capability only as **AI filters**; never call it Text-to-SQL or describe its internal mechanics.

## Voice and structure

- Study `records/stepper-orientation.html` before writing. Be declarative, settled, and specific to VC's analysts, care managers, and config admins working on large desktop monitors and long server-backed configuration tasks.
- State the rule, then the deciding question. Use Indian/British spelling: organise, colour, prioritise, behaviour.
- Write for scanning. Lead paragraphs, principles, section intros, and demo `.note`s with a short bold claim. Keep each block to one idea and 1–3 short sentences.
- Use `ul.clean` for parallel enumerations; never put a `<ul>` inside a `<p>`. In `.principles`, use a claim-led `<h3>` plus one short line or a 2–3 item list.
- Prefer direct numerals and terse directives, for example: “Past 4 fields → full page”. Use `→` sparingly, only for promotion decisions.
- Avoid hedging, clinical disclaimers, AI disclaimers, filler, and abstract best-practice claims.

## Verification

There are no tests or CI. Before finishing, inspect the site at a wide and narrow viewport: open `index.html` from disk and click through relative links, or run `python3 -m http.server` because headless browsers block `file://`. Confirm fonts and palette render, the shell is consistent, status badges match, comparisons stack on narrow screens, tables become labelled rows, and grids collapse to one column.
