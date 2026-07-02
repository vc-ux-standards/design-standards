# Spacing, padding & proportion fixes — VC Design Standards

## Context

After the Radix re-palette + nav-fix pass (PR #11, #12), the index page still has
visible spacing/padding/proportion problems. Screenshots show three concrete
defects plus a general sense of drifting rhythm. Diagnosis against the live
render (served at `localhost:8791`) plus a full read of `css/base.css` and
`css/tokens.css` traced these to **one root cause and three specific bugs**:

- **Root cause — no spacing scale.** `tokens.css` defines only `--measure` and
  `--gutter`. Every other spacing value in `base.css` is an ad-hoc literal
  (`0.32rem`, `0.85rem`, `1.15rem`, `1.6rem`, `2.75rem`, …). Nothing snaps to a
  common rhythm, so gaps drift between components and tiers.
- **Bug — stray indigo divider under every register row.** The faint
  purple/lavender line in the screenshots is real. The global rule
  `a{ border-bottom:1px solid color-mix(in oklch, var(--cool) 35%, transparent) }`
  (`base.css:38`) leaks onto `.regrow` (which is an `<a>`). `.regrow` only sets
  `border-top`, never resets the inherited `border-bottom`, so each row carries
  an unintended indigo underline on top of its intended `--rule` divider.
- **Bug — nav hover highlight is full-bleed.** `.homenav .navrow` has
  `padding:0.32rem 0` (zero horizontal). On `:hover` the `--paper-2` background
  hugs the text on the left and runs past the count on the right, reading as a
  banner rather than a tidy nav item (user's CONTENTS screenshot).
- **Bug — the two-tier register rhythm crowds.** The `STANDARDS` parent heading
  sits only ~1.6rem above its first sub-group (`FORMS`), far tighter than the
  2.75rem between top-level groups, so `STANDARDS` reads as attached to `FORMS`
  instead of as a parent band.

The user chose to fix the **root cause** (add a spacing scale) as well as the
defects, and to file the work in a new Linear project. Record pages
(`stepper-orientation.html` etc.) render cleanly — the work is concentrated in
the index shell plus the shared `base.css` rules those pages also use.

Constraints (from `CLAUDE.md`): plain HTML+CSS, no JS/build, tokens only, visual
language is canonical — this **regularises** spacing onto a scale, it does not
redesign. Verify every change against the pre-change render so character holds.

---

## 1 · Add a spacing scale to `css/tokens.css`

Introduce a fixed-step rem scale (root font is 18px). Add under the existing
`--measure`/`--gutter` block:

```css
/* Spacing scale — fixed component rhythm. Fluid page rhythm stays on the
   clamp()s (--gutter, masthead/section) which are intentionally viewport-fluid. */
--space-1: 0.25rem;   /* hairline: dot↔text, tight label↔control */
--space-2: 0.375rem;  /* very tight */
--space-3: 0.5rem;    /* small gaps, inset padding */
--space-4: 0.75rem;   /* row inner padding, nav row */
--space-5: 1rem;      /* default block gap */
--space-6: 1.5rem;    /* card padding, card→card */
--space-7: 2rem;      /* sub-group separation */
--space-8: 2.75rem;   /* top-level register group gap */
--space-9: 4rem;      /* large block rhythm */
```

Keep `--measure` and `--gutter` as-is. Leave the two fluid `clamp()` rhythms
(`header.masthead`, `section`) fluid — do **not** flatten them to fixed steps.

## 2 · Fix the three defects (`css/base.css`)

**Defect A — stray indigo row border.** In `.regrow` (`base.css:282`) reset the
inherited anchor border and re-declare only the intended divider:
`border:0; border-top:1px solid var(--rule);`. Confirm `.regrow:hover{
border-color:var(--rule-strong) }` now recolours only the top edge. Audit the
other block-level anchor (`.dircard`, `base.css:223`) — it already sets a full
`border`, so it's clean; note the pattern so future row/card anchors reset border.

**Defect B — nav hover padding.** Give `.homenav .navrow` (`base.css:252`)
horizontal padding with a compensating negative margin so text still aligns to
the rail edge while the hover highlight insets and rounds — mirror the `.regrow`
pull-out idiom:
`padding:var(--space-2) var(--space-3); margin:0 calc(-1*var(--space-3));
border-radius:4px;`. Recompute the sub-row indent
(`.homenav .navgroup ul .navrow`, `base.css:257`) so the visual indent is
preserved on top of the new base padding. In the narrow chip-strip override
(`base.css:262–270`) reset the negative margin so it doesn't break the flex wrap.

**Defect C — two-tier register rhythm.** Widen the separation between a parent
`.reghead` and its first `.regsub` so the parent band reads as a tier: bump
`.regsub` (`base.css:279`) to `margin-top:var(--space-7)` and, if still tight,
target the parent's first sub-group specifically. Keep sub-group gap < the
`--space-8` top-level group gap so the hierarchy stays legible.

## 3 · Migrate index-shell + shared rhythm onto the scale (`css/base.css`)

Snap the ad-hoc literals in these components to the nearest scale step (values
shift slightly — that's the regularisation; verify against the render):

- **Home shell / nav** (`.homeshell` gap, `.navhead` margin, `.navrow` padding,
  `.navgroup` margin, `.glabel` padding, sticky `top`) → `--space-*`.
- **Register** (`.reggroup` `margin-top:var(--space-8)`, `.reghead`
  `padding-bottom:var(--space-4)`, `.regrow` `padding:var(--space-4)` with
  `margin:0 calc(-1*var(--space-4))`, `column-gap`, `row-gap`).
- **Record cards** (`base.css:60–67`) — harmonise the near-duplicates:
  `.context` and `.rulecard` both to `padding:var(--space-6)` and
  `margin-top:var(--space-6)` (today they differ: 1.4/1.5 vs 1.5/1.6, 2rem vs
  1.5rem). Also `.rechead` gap/margin, `.secnum` margin.

**Verify (minor) — badge↔title alignment.** `.regrow{ align-items:baseline }`
sits the mono pill slightly low against the serif title. After the above, check
the render; if it still reads low, nudge the status cell (e.g. `align-self` /
small offset). Do not over-engineer — this is a visual fine-tune, not a rewrite.

**Scope boundary:** migrate the *layout-level* rhythm above. Leave the schematic
demo internals (`.appframe`, stepper/`.v-rail`/`.h-bar`, form `.fld`/`.inp`,
chart `.viz-*`, page mockups `.pg`/`.screen`) on their current literals for now —
they aren't in the reported issues and touching them risks visual regressions
across 14 record pages. Flag them as a follow-up sweep if desired.

## Files to modify

- `css/tokens.css` — add the `--space-*` scale (§1).
- `css/base.css` — three defect fixes (§2) + scale migration of the index shell,
  register, and shared card/masthead rhythm (§3). No HTML changes required; the
  index inline masthead styles (`index.html:37,40`) may optionally move onto the
  scale but are existing spacing tweaks — leave unless trivially aligned.

---

## Deliverables beyond the code

1. **Repo plan file** — save this plan (durable) as
   `plans/2026-07-03-spacing-and-proportion-fixes.md`, matching the existing
   convention (`plans/2026-07-02-radix-repalette-and-nav-fixes.md`).
2. **Linear** — create project **"VC Design Standards"** under team **OKLCH**
   (id `e35868f9-cb30-4e25-bb28-840abb83ece5`). Create an issue in it —
   "Fix index spacing, padding & proportion issues" — with a concise description
   (the root cause + the three defects + acceptance criteria below). Attach the
   plan as a resource: once the plan file is committed and pushed, attach its
   GitHub blob URL via `create_attachment`; if not yet pushed, embed the plan
   body in the issue description as the fallback.

**Acceptance criteria (for the issue):**
- No indigo/lavender line under register rows; a single `--rule` divider only.
- Nav hover highlight is inset and rounded, not full-bleed; count not touching
  the edge.
- `STANDARDS` parent band clearly separated from `FORMS`; two tiers legible.
- All migrated components read from `--space-*`; no new hard-coded rem gaps in
  the touched rules.
- Index visual character unchanged at wide and narrow widths.

## Verification

Serve and eyeball (the only check — no tests/CI, per `CLAUDE.md`):

```
python3 -m http.server 8791   # then load http://localhost:8791/index.html
```

- **Wide (~1440px):** register rows show one clean divider (no indigo); hover a
  nav row → inset rounded highlight; `STANDARDS`→`FORMS` gap reads as a tier;
  badge sits level with its title.
- **Narrow (~500px):** nav folds to the chip strip without the negative-margin
  bleeding; register rows stack (status → title → summary); no horizontal
  overflow.
- **Record page** (`records/stepper-orientation.html`): `.context`/`.rulecard`
  rhythm even; masthead/section spacing unchanged.
- Open `index.html` straight from disk too (relative paths must still resolve).
