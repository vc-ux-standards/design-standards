# Plan — Move VC Design Standards off Optum colours → Radix, plus nav/affordance fixes (items 1–8)

## Context

The VC Design Standards site (plain HTML/CSS, no build) describes its palette as "not
Optum," but the two brand accents in `css/tokens.css` *are* Optum's colours — the comments
literally name **"Optum Orange #FF612B"** and **"Optum Interaction Blue ~#0F68B2"**. A design +
usability audit also found the type system is strong but navigation between/within records is
nearly absent and hover/focus affordances are too quiet.

This plan does two things, both authorised by the user (the colour change is a
`CLAUDE.md`-flagged "do not redesign the visual language" exception — **note the authorisation
in the commit message**):

1. **Re-palette** the brand + interactive accents off Optum to **Radix Plum** (brand) and
   **Radix Indigo** (interactive), and fix the downstream data-viz palette the swap disturbs.
2. Ship a batch of **CSS-only navigation / affordance / legibility fixes** (handoff items 5–8),
   plus the per-record eyebrow-link markup change.

Outcome: no Optum hue or name anywhere in the *live* site; a clearly-not-Optum but still-linky
interactive colour; and records that are navigable, with visible hover/focus and legible labels.

**Scope decided with the user:** handoff items **1–8** only. Item 9 (prev/next between sibling
records + sticky in-page section rail) is deferred to a separate, larger branch.

## Execution checklist

One item per commit, in order. tokens.css items (A1–A3) are sequential; base.css items
(B1–B4, C-css) are sequential; markup items (C1–C2) come last.

- [ ] **A1** — `css/tokens.css`: swap `--warm*`/`--cool*` triplets to Radix Plum/Indigo and scrub the Optum names/hex from comments (record the CLAUDE.md-flagged authorisation in the commit message).
- [ ] **A2** — `css/tokens.css`: decouple + retune the data-viz categorical set (`--cat-1`…`--cat-6`) and rewrite the now-false block comment.
- [ ] **A3** — `css/tokens.css`: retint `--div-high` to Plum and update the diverging block comment.
- [ ] **B1** — `css/base.css`: replace `.topnav .brand:hover{border:none}` with a visible wordmark hover affordance.
- [ ] **B2** — `css/base.css`: add hover bg-step + border + transition to `.homenav .navrow` and `.regrow`.
- [ ] **B3** — `css/base.css`: add global `a`/`.inp` `:focus-visible` rings and the `.skip` reveal CSS.
- [ ] **B4** — `css/base.css`: lift the smallest mono labels (`.sub`, `.pill`) and add scannable status-chip borders (transparent base border + visible border on `draft`/`superseded`).
- [ ] **C-css** — `css/base.css`: add `a.eyebrow` link-chrome reset + intentional hover.
- [ ] **C1** — 13 `records/*.html`: convert both `<span class="eyebrow">` occurrences to `<a class="eyebrow" href="../index.html#ANCHOR">` per the anchor table.
- [ ] **C2** — markup: add `id="main"` + skip link to `index.html`, all 13 records, and `records/_template.html`; update the template's eyebrow placeholder to the linked form with a `<!-- TODO -->` note.

## Constraints (from CLAUDE.md — hold these)

- Plain HTML + CSS. **No JS, no build, no package.json.** Must work from disk *and* GitHub Pages.
- **Relative paths everywhere** (`../css/base.css`, `../index.html#...`). Never absolute.
- **Reuse tokens; never hard-code a colour or measure.** New component style = flagged, not quiet.
- Status badges stay in sync between a record's `.rechead` masthead and its `index.html` `.regrow`
  (no status changes in this work, but don't desync them).
- IP: always "VC"; "AI filters" only, never its internals. Voice: declarative, British/Indian
  spelling (colour, organise). No copy is being written here beyond nav labels.

## Verified current state (checked against the code, not the handoff)

- `css/tokens.css` accents (declarations, not comment lines): `--warm` **line 16**
  `oklch(0.705 0.195 42)`, `--cool` **line 20** `oklch(0.525 0.115 245)`; full triplets lines
  16–18 and 20–22; Optum comments lines 14–15 and 19.
- Data-viz: `--cat-1` (l.43) = `var(--cool)`, `--cat-2` (l.44) = `var(--warm)` — **`var()` aliases,
  so they move automatically when brand changes.** `--cat-4` (l.46) = `oklch(0.550 0.140 330)`
  **purple at hue 330 — identical to the proposed Plum brand hue.** `--div-high` (l.61) =
  `oklch(0.640 0.170 42)` orange. `--seq-*` (l.51–55) and `--div-low` (l.59) are hue-245 blue
  **literals** (not aliases) — they will NOT auto-update; we leave them (data blues, not brand).
- `design-decisions.html` (repo root) carries the same two Optum comments (l.34, l.37) but is an
  explicit **DO-NOT-EDIT archive, nothing links to it** → **left untouched** (user decision).
- `css/base.css` (543 lines) affordance targets all confirmed: `.topnav .brand:hover` l.194
  (`border:none`), `.topnav .brand .sub` l.196 (`0.6rem`, `--ink-3`), `.dircard:hover` l.213
  (the good model: bg + border-colour), `.homenav .navrow:hover` l.242 (colour-only),
  `.regrow:hover` l.272 (`--paper-2`, ~3% step), `.inp:focus` l.323 (**only** custom focus —
  **zero `:focus-visible` anywhere**), `.vh` l.180 (legacy `clip` only, no focus-reveal partner),
  `.status` l.203 (`0.62rem`), `.status.draft` l.208 (`--ink-3` on `--paper-3`, no border; near-
  identical to `.status.superseded` l.209). `.status.proposed` (l.206) also consumes `--warm-soft`/
  `--warm-ink`/`--warm`, so the Plum swap recolours that legacy chip too — expected, fine.
- **Global `a` (l.38)** is `color:var(--cool); text-decoration:none; border-bottom:1px solid
  color-mix(…var(--cool)…)`, with `a:hover` l.39. Two consequences: (a) **all prose/reference links
  are already `--cool`** — the Indigo swap recolours the entire site's body links automatically, so
  `--cool`'s contrast is a site-wide (not just nav) concern; (b) `.eyebrow` (l.34) sets
  `color:var(--ink-3)` but **not** `border-bottom`, so converting an eyebrow `<span>`→`<a>` will
  inherit the link's border-bottom underline unless explicitly reset.
- `records/_template.html` carries the **old shell** — plain `<span class="eyebrow">` (l.33), no
  `id="main"`, no skip link. It is the documented add-a-record workflow (CLAUDE.md), so it must be
  updated in lockstep or every future record regresses.
- 13 real records (+ `_template.html`). Six index anchors exist:
  `#component-usage`, `#layout`, `#standards` (no rail link), `#forms`, `#responsive`, `#data-viz`.
- Eyebrow markup is `<span class="eyebrow">Category · Topic</span>`, appearing **twice** per record
  (topnav + `.rechead`) with identical text. Structurally identical across all 13 records, but
  **line numbers and indentation vary** (`breakpoints.html` is offset; three files indent `.rechead`
  8 spaces) → match on the eyebrow string, never on line number.

---

## Part A — Colour: move off Optum → Radix Plum + Indigo (`css/tokens.css`)

### A1. Swap the two accent triplets (lines 16–18, 20–22) and rewrite the comments (14–15, 19)

Replace the `--warm*` / `--cool*` block with the tuned Radix OKLCH approximations, and scrub the
Optum names/hex from the comments (public repo):

```css
/* Brand accent — Radix Plum. --warm-ink is the readable ink on --warm-soft
   (chips, table heads); parallels the --ok/--ok-ink pair. */
--warm:         oklch(0.55 0.18 330);   /* Radix Plum 9  — wordmark, chips, bullets */
--warm-soft:    oklch(0.94 0.035 330);  /* Radix Plum 3  — chip / table-tint background */
--warm-ink:     oklch(0.47 0.16 328);   /* Radix Plum 11 — readable text on --warm-soft */
/* Interactive — Radix Indigo. */
--cool:         oklch(0.51 0.16 267);   /* Radix Indigo 11 — links/interactive */
--cool-soft:    oklch(0.94 0.03 274);   /* Radix Indigo 3 */
--cool-ink:     oklch(0.45 0.15 267);   /* Radix Indigo 11/12 — text on --cool-soft */
```

These are **tuned OKLCH approximations of the Radix light-mode steps, not pixel-exact
conversions** — verify the final two accents in oklch.com or Huetone before committing (see
Verification). Paper / ink / rule / error / ok are untouched.

### A2. Fix the data-viz categorical set (lines 43–48) — decouple + retune (user decision)

Because `--cat-1`/`--cat-2` are `var()` aliases to the brand, the swap alone would make them
Indigo(267) + Plum(330) — only ~63° apart — and Plum(330) would collide with the existing
`--cat-4` purple(330). **Un-alias cat-1/cat-2 to their own literal data hues and retune the set
so adjacent series stay separable, keeping a warm CB anchor but off Optum-orange 42.** Candidate:

```css
--cat-1: oklch(0.55 0.13 250);   /* blue      — pinned literal, decoupled from --cool */
--cat-2: oklch(0.62 0.18 32);    /* vermilion — warm CB anchor, redder than Optum's 42 */
--cat-3: oklch(0.600 0.130 165); /* green   — unchanged */
--cat-4: oklch(0.550 0.140 315); /* purple  — nudged 330→315 to clear brand Plum */
--cat-5: oklch(0.620 0.100 200); /* teal    — unchanged */
--cat-6: oklch(0.700 0.120 85);  /* gold    — unchanged */
```

Rationale: this is essentially the original CB-safe 6-set with (a) cat-1 pinned as a literal blue
so it no longer follows the brand, (b) orange→vermilion (off Optum orange, keeps the deutan/protan-
critical warm anchor), (c) cat-4 nudged off the brand-Plum hue. **Fix the comment drift too:** the
block comment (l.34–41) still says "`--warm/--cool/--ok/--error` keep their existing brand and
semantic meanings and are reused here" and "`--cat-1/-2` reuse the brand hues so charts stay
on-brand" — both are now false for cat-1/cat-2; rewrite them to state cat-1/cat-2 are independent
data hues. Record the chosen OKLCH values inline in the comments so the palette is self-documenting
and the branch doesn't depend on unrecorded external tooling (see Verification for the light-touch
check).

### A3. Retint the diverging "over" pole `--div-high` (line 61) off orange (user decision)

Diverging scale is `--div-low` blue (under) ↔ `--div-mid` neutral ↔ `--div-high` warm (over).
Retint the warm pole to **Plum**, tying it to the new brand and removing the last orange:

```css
--div-high: oklch(0.55 0.18 330);   /* Plum — "over target"; was Optum-family orange (hue 42) */
```

Also update the diverging block comment (l.57–58) — "Cool = below, warm = above" — so the "above"
pole reads as Plum, not orange. Leave `--div-low` (l.59) and `--seq-*` (l.51–55) as their hue-245
data blues — they're ordered/diverging encodings, not brand, and stay internally consistent.
**Verify** the blue↔plum pair reads as opposite poles against the neutral mid, and that
`--div-high` is distinguishable from `--error` (`oklch(0.545 0.165 28)`) in chart context; if it
reads too close, use a warm red ~25 instead.

---

## Part B — CSS-only nav / affordance / legibility fixes (`css/base.css`)

Model every hover/focus change on the existing good pattern `.dircard:hover` (l.213: bg step +
border-colour). Add `transition: background 150ms, color 150ms, border-color 150ms;` to the base
rule of each interactive row.

- **B1 — Wordmark hover (item 5a).** Replace `.topnav .brand:hover{ border:none; }` (l.194) with a
  visible affordance on the `.wm`: colour shift and/or underline, e.g.
  `.topnav .brand:hover .wm{ text-decoration:underline; text-underline-offset:2px; }`. The wordmark
  is the only route back to the index — it must feel clickable.

- **B2 — Nav-row + register-row hover (item 6).** `.homenav .navrow:hover` (l.242): add a background
  step (`--paper-2`) + transition, matched to `.dircard`. `.regrow:hover` (l.272): change the near-
  invisible `--paper-2` to a clearer step (`--paper-3`) + a `border-color` nudge + transition.

- **B3 — Focus-visible rings + skip link (item 7).** Make the base mechanism **global**, not a list
  of named selectors — prose links (`a`, l.38) and form inputs (`.inp`, l.323) are real keyboard
  targets an enumerated list would miss. Add `a:focus-visible` and `.inp:focus-visible` with a
  shared ring tokened to the new `--cool` (`outline:2px solid var(--cool); outline-offset:2px;`).
  Then **only tune** `.regrow`, `.navrow`, `.dircard`, `.topnav .brand`, `a.eyebrow` where the
  outline offset/radius needs adjusting for the element's box. Keep `.inp:focus` (l.323) for the
  no-`:focus-visible` fallback. Add a **skip link**: a `.skip` class, visually-hidden (reuse the
  `.vh` pattern, l.180) until `:focus`, then revealed (`position:fixed; top:0; left:0; clip:auto;
  width:auto; height:auto; padding; background:var(--paper); outline`). Markup in Part C.

- **B4 — Lift the smallest mono labels + scannable status (item 8).** Bump the 0.6rem uppercase mono
  labels to ~0.66rem and darken to `--ink-2`: `.topnav .brand .sub` (l.196) and `.listrow .pill`
  (l.428). For the status chips, **avoid box-size jitter**: put `border:1px solid transparent` on
  the base `.status` rule (l.203) so every chip reserves the border box, then give the muted states
  `.status.draft` (l.208) and `.status.superseded` (l.209) a visible `border-color:var(--rule-strong)`
  (and set matching borders on the other states to `transparent` implicitly via the base rule).
  Leave `.status` font-size (0.62rem) as-is; the border does the scannability work.

---

## Part C — Eyebrow → index-anchor links (markup, per record)

Convert both `<span class="eyebrow">Category · Topic</span>` occurrences (topnav + `.rechead`) in
**each of the 13 records** to
`<a class="eyebrow" href="../index.html#ANCHOR">Category · Topic</a>`. **Match on the eyebrow
string, not line number** (line numbers/indentation vary). Anchor is per-record — the "Standards"
first-segment maps to `#standards` (no rail link), so **key the anchor on the real sub-category**:

| Record(s) | Eyebrow text | Anchor |
|---|---|---|
| `stepper-orientation.html` | `Component usage · Steppers` | `#component-usage` |
| `modal-vs-page.html` | `Layout · Containers` | `#layout` |
| `form-layout.html`, `form-labelling.html` | `Standards · Forms` | `#forms` |
| `breakpoints.html` | `Standards · Responsive` | `#responsive` |
| 8 data-viz records (`bar-and-column`, `choosing-a-chart`, `line-charts`, `ranges-and-dot-plots`, `colour-for-data`, `numbers-and-states`, `charts-at-tile-scale`, `reading-honestly`) | `Standards · Data visualization` | `#data-viz` |

- Within one file the two eyebrows share identical text, so a per-file `replace_all` on the exact
  `<span class="eyebrow">…</span>` string is safe.
- **Leave `index.html`'s own eyebrows as `<span>`** ("Overview", "VC · UX decision records") — they
  are the home page, not category links.
- **`a.eyebrow` must reset the global link chrome** (`base.css:38` gives every `a` a `--cool`
  colour + `border-bottom` underline): add `a.eyebrow{ color:var(--ink-3); border-bottom:0; }` (the
  `.eyebrow` class already overrides colour, but the border-bottom would otherwise show as a link
  underline). Add an intentional hover (e.g. `a.eyebrow:hover{ color:var(--cool); }`) so it reads
  as clickable, and let the global `a:focus-visible` from B3 supply the ring.

### Skip-link markup + template update (item 7)

- Add `id="main"` to each `<main>` and, as the first child of `<body>`,
  `<a class="skip" href="#main">Skip to content</a>` — uniform across `index.html` + all 13 records.
- **Update `records/_template.html` in lockstep** (it's the CLAUDE.md-documented add-a-record
  source): give it the `id="main"` + skip link, and change its placeholder
  `<span class="eyebrow">Category · Topic</span>` (l.33, topnav) and the matching `.rechead`
  eyebrow to the linked form `<a class="eyebrow" href="../index.html#ANCHOR">Category · Topic</a>`
  with a `<!-- TODO: set the category anchor -->` note, so future records inherit the new pattern.

---

## Files touched

- `css/tokens.css` — accents (16–22), comments (14–15, 19), categorical set (43–48 + comment 41),
  `--div-high` (61).
- `css/base.css` — B1–B4 + `a.eyebrow` reset + global `a:focus-visible`/`.inp:focus-visible` +
  `.skip` reveal + base `.status` transparent border.
- 13 `records/*.html` — eyebrow spans → anchors (2 each), `id="main"` + skip link.
- `records/_template.html` — same shell update (skip link, `id="main"`, linked-eyebrow placeholder)
  so future records don't regress.
- `index.html` — `id="main"` + skip link only (eyebrows stay spans).
- **Not** `design-decisions.html` (archive, left untouched by decision).

## Alternative considered — semantic token aliases (deferred)

A cleaner long-term shape is to introduce `--brand*` / `--interactive*` semantic tokens and map
`--warm: var(--brand); --cool: var(--interactive);`. Deliberately **not** doing it here: the user
authorised a *value* swap, not a token-vocabulary redesign; `--warm`/`--cool` are referenced
throughout `base.css`, so renaming widens the diff against the handoff's explicit "keep it minimal,
respect durability" intent. Worth a separate, self-contained cleanup branch if desired.

## Verification (no tests / CI — visual + tooling from a served copy)

`file://` is blocked headless — serve first: `python3 -m http.server 8099`, open
`http://localhost:8099/index.html`. Check one **wide (~1440)** and one **narrow (~460)** viewport
(narrow is where the home rail folds to a chip strip <760px, tables stack, grids collapse).

1. **Palette swap** — wordmark, inline prose/reference links, `.status` chips, `.tbl` warm/cool
   column tints all render Plum/Indigo. The correct scrub check is **no Optum names/hex and no brand
   accent on an old Optum hue** — not "no orange/azure" (vermilion cat-2 hue 32, `--error` hue 28,
   and the seq/div data-blues at hue 245 legitimately remain). `grep -riE 'optum|FF612B|0F68B2'`
   over everything except `design-decisions.html` (archive) and `.claude/handoffs/` returns nothing;
   confirm `--warm` is no longer hue ~42 and `--cool` is no longer hue ~245.
2. **Contrast** — confirm new `--cool` (`oklch(0.51 0.16 267)`), the **site-wide body-link colour**,
   clears **APCA ~Lc 60** as link text on `--paper` (one tool: oklch.com or apcacontrast.com).
   Confirm `--warm-ink` on `--warm-soft` and `--cool-ink` on `--cool-soft` stay readable.
3. **Data-viz** — screenshot `records/colour-for-data.html` (+ `choosing-a-chart.html`) from the
   served copy: categorical swatches are distinguishable and the diverging scale reads
   blue↔neutral↔plum with `--div-high` ≠ `--error`. Run **one** deutan/protan simulation of the 6
   swatches if a simulator is handy, but the branch's gate is the served screenshots + the OKLCH
   values recorded in the token comments — not a chain of external tools.
4. **Affordances** — hover the wordmark, nav rows, register rows, dircards: each shows a visible
   bg/border/underline change with a smooth transition. Tab through with a keyboard: prose links,
   form inputs, nav/register rows, dircards, the brand and the eyebrow **all** show the Indigo
   `:focus-visible` ring (the global rule is why prose links and inputs are covered); the skip link
   appears on first Tab and jumps to `#main`.
5. **Eyebrow links** — click an eyebrow in one record per category; it lands on the right index
   anchor and shows **no link-underline chrome** (the `a.eyebrow` reset). Spot-check
   `breakpoints.html` (offset markup) and a data-viz record (deep sub-anchor).
6. **Legibility & no jitter** — the topnav "Design Standards" sub and pill labels read comfortably;
   the `draft`/`superseded` chips now have a visible border and are distinct from `adopted`; confirm
   chips do **not** shift size between states in the register rows (the base transparent border).
7. **Template** — `records/_template.html` opens with the skip link, `id="main"`, and the linked-
   eyebrow placeholder, so a duplicated record inherits the new pattern.

Commit message must **record the user's authorisation** for the CLAUDE.md-flagged palette change.

## Deferred (not in this plan)

- **Item 9** — prev/next between sibling records + sticky in-page section rail on long records,
  reusing the home `.homenav` sticky pattern. Separate, larger branch (touches every record's body).
