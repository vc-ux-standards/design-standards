# Session Handoff: Move VC Design Standards off Optum colours → Radix, plus nav/affordance fixes

**Date:** 2026-07-02
**Project:** /Users/sudakshsoti/dev/design-patterns (VC Design Standards — static HTML/CSS site of UX decision records)
**Branch:** main (clean at handoff). Remote: `github.com/sudakshsoti/design-standards`. GitHub Pages URL: https://sudakshsoti.github.io/design-standards/
**Phase:** Planning complete → ready to implement. No files changed yet.

## Current State

**Task:** Re-palette the site away from Optum's brand colours to Radix Colors, and ship a batch of CSS-only navigation/affordance/label fixes surfaced in a design audit.
**Progress:** Audit done, colour direction decided, plan agreed. Zero implementation started. Next session executes the TODO list below.

## What We Did

Ran a design + usability audit of the live site (served locally, viewed at wide and narrow). Found the type system is strong but navigation between/within records is nearly absent and hover/focus affordances are too quiet. Discovered the two brand accents are literally Optum's colours (named in `tokens.css` comments). User decided to move to Radix Plum (brand) + Radix Indigo (interactive), fully off anything Optum-like.

## The core finding (why we're doing this)

The palette *is* Optum, despite being described as "not Optum":
- `css/tokens.css:14` — `--warm` accent is commented **"Optum Orange #FF612B"** (`oklch(0.705 0.195 42)`).
- `css/tokens.css:19` — `--cool` accent is commented **"Optum Interaction Blue ~#0F68B2"** (`oklch(0.525 0.115 245)`).
Those are the only two "Optum" references in the whole repo (grep for `optum|FF612B|0F68B2` returns just these two comment lines). The Optum-ness is purely those two hues + the naming comments.

## Decisions Made

- **Use Radix Colors** — battle-tested, semantic 12-step scales, pairs with the Tailwind v4 + shadcn stack the user works in. Chosen over Tailwind's default palette because Tailwind's default accents run high-chroma (blue-600 ≈ chroma 0.245) and read loud against this muted warm-paper editorial base.
- **Brand = Radix Plum, Interactive = Radix Indigo** — "move away from anything that remotely looks like Optum" rules out the orange brand *and* a mid-azure interactive blue (Optum's is hue ~245). Interactive shifts violet-ward (Indigo, hue ~267) so it's clearly not Optum's azure but still reads as a link. Brand leaves the warm family entirely (Plum, hue ~330).
- **Only the two accent triplets change.** Paper, ink, rule, error, ok all stay. Keeps the change ~6 lines and respects the site's durability intent.
- **Colour change is a `CLAUDE.md`-flagged decision, not a quiet edit.** `CLAUDE.md` declares the visual language canonical ("do not redesign it"). User has explicitly authorised this specific change, so it's approved — note the authorisation in the commit message.

## The exact palette to apply

Replace the `--warm*` / `--cool*` triplets in `css/tokens.css` (currently lines ~14–22) with:

```css
/* Brand accent — Radix Plum. --warm-ink is the readable ink on --warm-soft
   (chips, table heads); parallels the --ok/--ok-ink pair. */
--warm:         oklch(0.55 0.18 330);   /* Radix Plum 9  — wordmark, chips, bullets, chart cat-2 */
--warm-soft:    oklch(0.94 0.035 330);  /* Radix Plum 3  — chip / table-tint background */
--warm-ink:     oklch(0.47 0.16 328);   /* Radix Plum 11 — readable text on --warm-soft */
/* Interactive — Radix Indigo. */
--cool:         oklch(0.51 0.16 267);   /* Radix Indigo 11 — links/interactive, ~APCA Lc 65 */
--cool-soft:    oklch(0.94 0.03 274);   /* Radix Indigo 3 */
--cool-ink:     oklch(0.45 0.15 267);   /* Radix Indigo 11/12 — text on --cool-soft */
```

**These are tuned OKLCH approximations of the Radix light-mode steps, not pixel-exact conversions.** Verify the final two accents in oklch.com or Huetone (huetone.ardov.me) before committing, and confirm `--cool` clears APCA Lc ~60 as link text on `--paper` (`oklch(0.976 …)`).

## TODO — execute in this order

### Colour: move off Optum → Radix Plum + Indigo
1. [ ] **Swap the two accent triplets** in `css/tokens.css` to the block above. Paper/ink/rule untouched.
2. [ ] **Rewrite the two Optum comments** (`tokens.css:14`, `tokens.css:19`) to reference Radix Plum / Radix Indigo. They name Optum + hex in a public repo — scrub regardless.
3. [ ] **Decide on the last orange:** `--div-high` is `oklch(0.640 0.170 42)` (orange) in the diverging data scale (`tokens.css:61`). Warm-pole-for-"over" is a legit data-viz convention, so this is a *decision*, not an automatic change — ask the user: keep as convention, or retint (e.g. toward Plum/red). Do not silently change.
4. [ ] **Re-check the data-viz categorical set.** `--cat-1` aliases `--cool` and `--cat-2` aliases `--warm` (`tokens.css:44-45`). Now Indigo + Plum sit only ~60° apart — weaker series separation than blue+orange gave. Either pin `--cat-1`/`--cat-2` to their own hues, or reorder the categorical ramp so adjacent series stay distinguishable. Re-run a colourblind check on the 6-colour set. Open the data-viz records to eyeball (e.g. `records/colour-for-data.html`, `records/choosing-a-chart.html`).

### Quick wins — navigation & affordance (all CSS-only, `css/base.css`)
5. [ ] **Give the wordmark a real hover + make the category eyebrow a link.**
   - `base.css:194` currently `.topnav .brand:hover{ border:none; }` — the *only* route back to the index (the "VC" wordmark) has zero hover feedback. Replace with a visible hover (e.g. underline via `border-bottom` on the `.wm`, or colour shift).
   - The category eyebrow is an inert `<span>` in every record (e.g. `records/stepper-orientation.html:21` topnav and `:30` masthead rechead). Make it an `<a>` linking to `index.html#<category-anchor>`. NOTE: this touches record markup, so it's a find/replace across all 13 record files, not just CSS. Confirm the anchor IDs exist on `index.html` (the home rail already links to `#component-usage` etc. — reuse those IDs).
6. [ ] **Strengthen nav-row + register-row hover** to `.dircard` level.
   - `.homenav .navrow:hover` (`base.css:242`) only nudges `--ink-2`→`--ink`. Add a visible background step + `transition` (~150ms).
   - `.regrow:hover` (`base.css:272`) shifts to `--paper-2` (`0.948` vs `0.976` paper — ~3% step, near-invisible). Make it a clearer step and add the transition.
   - Model to match: `.dircard:hover` (`base.css:213`) already does bg + border-colour well.
7. [ ] **Add `:focus-visible` rings** tokened to the new `--cool`, on `.navrow`, `.regrow`, `.dircard`, and `.topnav .brand`. Only form inputs have custom focus today (`.inp:focus`, `base.css:323`). Add a skip-link using the existing `.vh` helper (`base.css:180`).
8. [ ] **Lift the smallest mono labels.** Many IBM Plex Mono uppercase labels run 0.6–0.72rem in `--ink-3` (`oklch 0.62` on paper ≈ APCA Lc ~48, under the readable floor for that size). Worst: topnav `.sub` "Design Standards" at 0.6rem (`base.css:196`), `.status` chips at 0.62rem. Either `--ink-3`→`--ink-2` or bump the smallest to ~0.66rem. Give the `draft` status chip (`base.css:208`, `--ink-3` on `--paper-3`) a hairline border so status is scannable.

### Medium — structural (touches record markup)
9. [ ] **Add prev/next between sibling records** and a lightweight sticky in-page section rail on long records, reusing the home rail's markup/sticky pattern (`.homenav`, `base.css:238-259`). Records currently have no in-page nav despite numbered `01/02/03` sections; the home page's rail is the pattern to copy.

## Constraints to respect (from CLAUDE.md — read it first)

- **Plain HTML + CSS only. No JS, no build, no package.json, no framework.** Must work opened from disk *and* on GitHub Pages.
- **Relative paths everywhere** (`../css/base.css`, never `/css/...`).
- **Reuse tokens; never hard-code a colour or a measure.** New component style = a conversation, not a quiet addition.
- **Status badges must stay in sync** between a record's `.rechead` masthead and its `index.html` `.regrow` row.
- **IP:** always write "VC", never the full product name or parent org. The NL registry feature is "AI filters" only — never describe its internals.
- **Voice:** declarative, settled, no hedging; Indian/British spelling (colour, organise); lead every block with its claim. Study `records/stepper-orientation.html` before writing any copy.

## How to verify on resume

- Serve the folder (file:// is blocked in headless browsers): `python3 -m http.server 8099` then open `http://localhost:8099/index.html`.
- Check one wide (~1440) and one narrow (~460) viewport. Narrow is where the home rail folds to a wrapping chip strip (<760px), tables stack to labelled rows (<720px), grids collapse to one column.
- There are no tests and no CI — visual inspection from disk/localhost is the check.
- After the palette swap, spot-check: home wordmark, inline links, status chips, the `.tbl` warm/cool column tints, and at least one data-viz record's chart colours.

## Suggested execution shape

Items 1–2 and 5–8 are a clean first branch (colour swap + comment scrub + all CSS quick wins; item 5's eyebrow-link part adds a small record-markup find/replace). Items 3–4 need a quick user decision on the diverging orange and the categorical separation. Item 9 is a separate, larger branch. User's last question was "start the branch with 1–8?" — confirm scope, then branch off `main`.

## Files to review on resume

- `CLAUDE.md` — the working contract (constraints, voice, record anatomy). Read first.
- `css/tokens.css` — all colour/measure tokens; lines 14–22 (accents), 42–62 (data-viz palette). The whole edit centres here.
- `css/base.css` — every component style; hover/focus/label fixes live here. Key lines cited in TODOs 5–8.
- `records/stepper-orientation.html` — the reference record (voice + shell + topnav/eyebrow markup at lines 15–30).
- `index.html` — home rail + register; source of the category anchor IDs needed for TODO 5.
- `README.md` — full add-a-record workflow + status lifecycle.
