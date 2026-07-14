# CSS/HTML audit remediation — 7 small commits

## Context

A full audit of this static pattern-library site (plain HTML/CSS, no JS/build/deps) found the debt concentrated in: (a) 61 inline `style=""` attributes that are literal no-ops, (b) one record carrying a private copy of chart primitives that `base.css` later generalised, (c) dead selectors — one of which contradicts an adopted record — plus an orphaned origin file, (d) un-tokenised radii/shadows/spacing values retyped by hand, and (e) a three-way split in the site's own name. The goal: make the system read as designed-once-and-reused, in small independently revertable commits on this branch.

**Decisions settled with the user:**
- Full scope (all audit items).
- Canonical name: **"Design Standards"** everywhere.
- Radii consolidate to **4 tokens** (2/4/6/8px); 3px and 5px snap to 4px (accepted 1px visible change).
- The 2.5rem block gap snaps to **`--space-8` (2.75rem)** (accepted +4px visible change).
- **Delete** `design-decisions.html` and its 5 single-consumer selector groups.
- (My calls, low-stakes:) focus outline standardises on `--cool`; the 3 data records' hand-padded charts migrate to `.viz-plot`; `.viz-area`/`.viz-pos`/`.viz-ink` stay as reserved viz vocabulary with a comment.

**Hard constraints (unchanged):** no JS, no tooling, no new components without flagging, relative paths, don't touch the protected masthead `style="margin-top:1.5rem;"` on `.lead` (15×) — every sweep below explicitly excludes it. Palette/type/measure untouched.

Files: `css/tokens.css`, `css/base.css`, `index.html`, `records/*.html` (16 + `_template.html`), `CLAUDE.md`, delete `design-decisions.html`.

## Progress

- [x] Commit 1 — Delete no-op inline styles; move 2 colours into classes; housekeeping
- [x] Commit 2 — Dead CSS, orphan file, focus-rule conflict
- [x] Commit 3 — Migrate breakpoints.html off its private `.bp-*` copy
- [x] Commit 4 — Site-name sweep → "Design Standards"
- [x] Commit 5 — Spacing rhythm: contextual rules replace inline margins
- [x] Commit 6 — Radius + shadow tokens; base.css consolidation
- [ ] Commit 7 — Interaction polish, `<code>` rule, chart padding

---

## Commit 1 — Delete no-op inline styles; move 2 colours into classes; housekeeping
*Rendering verifiably identical.*

1. Remove `style="background:var(--cool-soft);"` (42×) and `style="background:var(--warm-soft);"` (3×) from `<span class="verdict tag v/h">` across `records/*.html` — `.tag.v`/`.tag.h` (`base.css:52-53`) already set exactly these; nothing later overrides them.
2. Remove `style="margin-top:1rem;"` from every references `<ul class="clean refs prose">` (16×) — `ul.clean` already sets `margin:1rem 0 0` (`base.css:160`).
3. Add `color:var(--paper);` to `.choice.on .mk` (`base.css:385`); remove the 6× `style="color:var(--paper)"` on choice-tick SVGs (e.g. `records/form-layout.html:386`).
4. Add `.viz-tile .delta.mute{ color:var(--ink-3); }` beside `.up`/`.down` (`base.css:512-513`); swap the 4× `style="color:var(--ink-3);"` in `records/numbers-and-states.html` for `class="delta mute"`. (Flagged: variant of an existing family, not a new component.)
5. Delete the leftover "HOW TO ADD A DECISION RECORD" template comment from `records/stacked-bars.html:2-11`.
6. `CLAUDE.md`: update the JavaScript paragraph — "There are none so far" is stale; name `records/stacked-bars.html` §03 as the one sanctioned inline script.

## Commit 2 — Dead CSS, orphan file, focus-rule conflict
*Rendering identical except input focus-ring colour (ink → cool).*

1. Delete `design-decisions.html` (root). Then delete its single-consumer selectors from `base.css`: `.wrap` (:21), `.status.proposed` (:217), `.status.contested` (:218), `.dirgrid`/`.dircard`/`.dt`/`.dr`/`.dircard:hover` (:222-227) and the `.dirgrid` line inside the `@media (max-width:900px)` block (:233-235).
2. Delete verified-unused selectors: `.fld .hint` + `.hint.err` (:334-336 — contradicts the adopted form-labelling rule "no hint line"), `.formcol` (:326), `.formdemo.lede` (:322), `.railstepper .ph` + `.ph:first-child` (:129-130), `.w-day` (:351), `.w-full` (:358).
3. Keep `.viz-area`, `.viz-pos`, `.viz-ink` — add a one-line comment marking them reserved viz vocabulary for future data records.
4. Focus conflict: delete `.inp:focus{ outline:2px solid var(--ink); … }` (:341). The earlier `a:focus-visible, .inp:focus-visible` cool rule (:40-41) — currently dead because :341 wins — becomes the single focus style. (Text inputs always match `:focus-visible`, so coverage is unchanged.)

## Commit 3 — Migrate breakpoints.html off its private `.bp-*` copy
*Subtle deltas on one record: gaps/paddings shift ≤0.15rem, bar tint 50→55% mix, KPI numeral 1.35→1.5rem.*

1. In `records/breakpoints.html` §03 + the Avoid/Use compare: `.bp-board`→`.viz-board`, `.bp-grid.cN`→`.viz-grid.cN`, `.bp-tile`(+`.wide`)→`.viz-tile`(+`.wide`), `class="bars"`→`class="viz-bars"`, `class="line"`→`class="viz-line"` (all equivalents at `base.css:497-521`; `rect.hi` works as-is via `.viz-bars rect.hi`). Delete the entire inline `<style>` block (:12-32).
2. §02 KPI row (:181-185): keep the flex wrapper's single layout inline (matches repo idiom for one-off tweaks); rebuild each of the 4 tiles as `.viz-tile` with `.h` (label) + `.big` (numeral), deleting the 12 repeated inline style attributes.
3. Also swap the one inline-styled `<code>` (:209) to a bare `<code>` (styled in Commit 7 — fine to be momentarily unstyled, or reorder 7 before 3 if preferred).
4. `CLAUDE.md`: rewrite the sanctioned-`<style>` exception paragraph — the `.bp-*` precedent is retired; `records/stacked-bars.html`'s scoped `.sb-*` block is now the only style-block exception.

## Commit 4 — Site-name sweep → "Design Standards"

1. 15 record `<title>`s + `records/_template.html`: suffix "· Design Decisions" → "· Design Standards".
2. Topnav `.sub` in `records/form-layout.html:20` and `records/form-labelling.html`: "Design Decisions" → "Design Standards".
3. `index.html:49`: `<h1>Design decisions</h1>` → `Design standards`.
   (Body prose that discusses "decision records" as a concept stays.)

## Commit 5 — Spacing rhythm: contextual rules replace inline margins
*+4px of air at ~20 spots (2.5rem → --space-8 2.75rem), per sign-off. Masthead leads untouched.*

1. `base.css` additions:
   - `:is(.demo, .compare) + :is(.demo, .compare){ margin-top:var(--space-8); }` — covers the second-demo/second-compare pattern (most of the 15 block instances).
   - `.prose ul.clean + p{ margin-top:var(--space-7); }` — covers the 5× "Reject in review"-style `<p style="margin-top:2rem;">` after a list.
   - One utility for the rest: `.mt-lg{ margin-top:var(--space-8); }` — for the 4× unclassed `<h3 style="margin-top:2.5rem;">`, the demo/compare instances not preceded by a sibling demo/compare (e.g. `records/stacked-bars.html:218`), and the lone 2.25rem (`breakpoints.html:251`).
2. Sweep `records/*.html`: delete the now-covered inline `margin-top` attributes; apply `class="… mt-lg"` where the contextual rules don't reach. Leave: the protected masthead leads (1.5rem ×15, 1.4rem index), genuinely one-off values (0.85, 0.4, 0.35rem…), and the 1.75rem cases (they match `.demo`/`.compare` defaults — just delete those attrs, they're near-no-ops).

## Commit 6 — Radius + shadow tokens; base.css consolidation
*Visible: 3px→4px on inputs/buttons/fields, 5px→4px on nav pills/rail steps/sb-card, `.pg` shadow blur 18→24px — all signed off / subtle.*

1. `css/tokens.css`: add
   - `--radius-1:2px; --radius-2:4px; --radius-3:6px; --radius-4:8px;`
   - `--shadow-frame: 0 1px 2px oklch(var(--shadow-ink) / 0.04), 0 8px 24px oklch(var(--shadow-ink) / 0.05);`
   - `--shadow-pop: 0 16px 48px oklch(var(--shadow-ink) / 0.32);`
2. `css/base.css`: replace every px radius with its token — 2px→`--radius-1`; 3px, 4px, 5px→`--radius-2`; 6px→`--radius-3`; 8px→`--radius-4`. Keep `50%` and the 1px "squared-dot" literals (`ul.clean.anti li::before`, `.compare .judge .d`) as-is.
3. Consolidate duplicate declarations with grouped selectors (no new classes):
   - `.appframe, .pg, .viz-board{ border/radius/overflow/background/box-shadow:var(--shadow-frame) }` — `.pg`'s 6px/18px shadow snaps to the shared token; per-component rules keep only their extras.
   - `.context, .rulecard{ … }` shared frame (border, background, padding, max-width, margin-top); `.rulecard` keeps its `border-left` override.
   - Chip family: group the genuinely identical subset of `.tag, .status, .judge, .listrow .pill, .syscard .verdict` (inline-flex, mono family, uppercase, `--radius-1`, line-height 1); per-chip size/tracking/padding stay put.
   - `.dialog` shadow → `var(--shadow-pop)`. `.drawer` (directional) and `.sb-card` stay literal.
4. `records/stacked-bars.html` scoped `.sb-*` block: swap its `border-radius:5px`/`2px` and card shadow for the new tokens (tokens are in scope there; the block stays sanctioned).

## Commit 7 — Interaction polish, `<code>` rule, chart padding

1. `base.css` typography section: add `code{ font-family:"IBM Plex Mono", ui-monospace, monospace; font-size:0.86em; }` — codifies the patch breakpoints.html made inline (~30 bare `<code>` across records, 18 in `records/bar-sizing.html`).
2. Transitions: `.step-circle` (:99) `transition:all .2s ease` → named properties at the house 150ms (`background, color, border-color, box-shadow`); add `transition:border-bottom-color 150ms` to `a` (:38) so link hover stops snapping.
3. Chart padding: in `records/charts-at-tile-scale.html`, `records/numbers-and-states.html`, `records/colour-for-data.html`, replace the 8× `<div class="chart" style="padding:0.6rem 0.7rem;">` with `class="chart viz-plot"` (keep `.chart` — it carries the svg display rules; `.viz-plot` is the canonical padding, `base.css:502`). Subtle: charts gain ~0.3rem breathing room, now consistent site-wide.

---

## Verification (after each commit; full pass at the end)

No tests/CI — the check is visual inspection, per CLAUDE.md.

1. **Grep gates** (zero-hit assertions after the relevant commit):
   - `grep -rn 'style="background:var(--cool-soft);"' records/ index.html` → 0 (C1)
   - `grep -rn 'bp-board\|bp-grid\|bp-tile' records/` → 0 (C3)
   - `grep -rn 'Design Decisions' records/ index.html` → 0 in `<title>`/`.sub` (C4)
   - `grep -rn 'dirgrid\|dircard\|\.formcol\|\.hint' css/base.css` → 0 (C2)
   - `grep -c 'border-radius:[0-9]' css/base.css` → 0 except 50%/1px literals (C6)
   - Protected check: `grep -c 'lead prose" style="margin-top:1.5rem;"' records/*.html` still **15** after every commit.
2. **Serve and click through** (`python3 -m http.server`, headless `file://` is blocked): index → each shelf anchor → spot-open `stepper-orientation`, `breakpoints` (the migrated record — compare §02/§03 against pre-change screenshots), `stacked-bars` (hover/focus/tap/Escape on the live tooltip demo still works), `form-layout` (tab into inputs — focus ring now cool). One wide viewport + one narrow (~500px: tables stack to labelled rows, grids collapse, compare blocks stack).
3. **Badge sync**: statuses in each record masthead still match its `index.html` `.regrow` (untouched, but confirm no accidental edits).
4. `git diff --stat` per commit should stay small and scoped; anything touching an unexpected file is a flag to stop.
