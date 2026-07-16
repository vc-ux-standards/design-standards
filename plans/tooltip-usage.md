# Plan: Tooltip usage — a new decision record

## Checklist

- [x] 1. Add the `.tt-*` schematic demo-tooltip primitive to `css/base.css` (spec in "The `.tt-*` primitive" below — tokens only, normal flow, no positioning)
- [x] 2. Create `records/tooltip-usage.html` from `records/_template.html` — full record per "The record" below (masthead, §01 ladder table, §02 four demos, §03 principles, §04 precedent + reco, §05 references)
- [ ] 3. Wire `index.html`: append the Tooltip usage `.regrow` as the fifth row under `#components`, bump the Components homenav count 4→5
- [ ] 4. Verification pass: serve locally, check both records' pages render, links relative, badges in sync, `data-l` on every td, no `<script>`/scoped `<style>`, wide + narrow viewport behaviour

## Context

VC's design system supplies a tooltip component (near-black bubble, near-white text) but no usage guidance. Designers oversize tooltips, fill them with headings and bulleted text, and disagree on when a tooltip is right at all. This record settles: when to use a tooltip vs not, the alternatives when it's wrong, the size/character ceiling, and whether buttons/links belong inside (they don't — that's a toggletip/popover). Show-don't-tell: a skimming reader gets every rule from the demos alone.

Research across Carbon, Material 3, Polaris, Atlassian, Spectrum, Fluent 2, NN/g, ARIA APG and WCAG 1.4.13 found near-total consensus (non-essential plain text only; no interactive content — tooltips never receive focus; brief; hover+focus triggers, Esc dismiss, hoverable/persistent per WCAG 1.4.13; never redundant with a visible label). The two genuine splits the record settles: Material alone allows buttons in "rich persistent tooltips" (but wires them `aria-haspopup="dialog"` — a popover in all but name), and disabled controls (Atlassian never / Fluent yes; we side with Atlassian + Spectrum: helper text instead).

## Decisions locked with the user

- Copy stays generic — "our tooltip component", never the internal design-system name, never the product name.
- **All demos static** — bubbles drawn permanently open, no `:hover`, no JS, no scoped `<style>`.
- **Demo CSS lives in `css/base.css`** as a shared demo-tooltip primitive.
- **House cap: max-width 240px; 1–2 short sentences, ≤ ~110 characters; no bullets, headings, images, buttons or links.**

## The record

**Shell**: `<article id="tooltip-usage">`, title `Tooltip usage · Design Standards`, h1 `What belongs in a tooltip<br>— and what never does`. Status **Draft** in both `.rechead` and the index `.regrow`; rulecard marked "(provisional)". Eyebrow `Components · Tooltips` → `../index.html#components` (matches "Components · Forms" phrasing in form-labelling.html:22).

**Masthead** (draft copy, refine in place; Indian/British spelling; match the register of records/stepper-orientation.html and records/modal-vs-page.html exactly):
- Lead: "A tooltip is a label, not a container. Ours carries one short line of helpful, non-essential text — a control's name, a shortcut, a term's definition. The moment it wants a second idea, a bullet, or a button, it has stopped being a tooltip; this record says where that content goes instead."
- `.context`: dense analytical surfaces, pointer on large monitors — tooltips cheap to reach, tempting to overload. Bold the deciding constraint: **a tooltip is invisible until asked for; nothing an analyst or admin needs to finish a configuration can live only there.**
- `.rulecard` (short `<p>` + `ul.clean` + closing `<p>`, modal-vs-page shape): one plain line of non-essential text · 1–2 short sentences ~110 chars, 240px cap, no bullets/headings/images · no buttons/links — an action makes it a toggletip/popover · hover AND focus trigger, stays while hovered, Esc dismisses, never on a disabled control (helper text instead). Closing: "The deciding question is: **can the user finish the task if the tooltip never appears?** If not, the content moves down the ladder — helper text on the page, a toggletip, or its own page."

**§01 Quick reference — the ladder as a table.** h2 "One question decides it: can they finish without it?" Prose opener names rungs 0 and 4 (visible label / documentation page) so the table stays 3 columns. **`.tbl.tri`** (plain `.tbl` is locked to 2 tinted columns), header chips `tag n` Inline helper text · `tag v` Tooltip · `tag h` Toggletip / popover. Rows: What it carries / Visible when / Interactive content / Length / Typical use here. **Every `<td>` gets `data-l="…"`** for the ≤720px stacked view.

**§02 The pattern in practice — four demos.** h2 "Three jobs — and everything else moves out". Opener: "**A tooltip has exactly three jobs: name, shortcut, definition.**"

1. **Use-only `.demo`** — "Registry toolbar · the three legitimate tooltips": one `.appframe` ("Patient registry") with a `.tt-row` of three open bubbles — icon button → `Export as CSV`; icon button drawn with focus ring (`.tt-iconbtn.focused`) → `Save view` + `.tt-kbd` ⌘S; dotted-underline term `PMPM` → `Per member per month — total cost divided by member months.` Note: hover and keyboard focus both open the same bubble; the screen works if none ever appears.
2. **`.compare`** — oversized bulleted tooltip vs the one-liner. Same trigger both sides (a `.formdemo` field "Attribution window" with a `.tt-help` "?"). Avoid: `.tt-bubble.wide` (420px) with faux heading + 3-bullet `<ul>` inside the dark bubble. Use: capped bubble, "Days after the index event during which claims still count." Notes carry the 240px cap.
3. **`.compare`** — essential info hidden in a tooltip vs inline helper text. Avoid: "Value set OID" field whose required format exists only in a bubble. Use: the same field as plain `.fld` + `.lbl` + `.help` + `.inp`, no tooltip at all. Note cross-references the Descriptions and placeholders record (records/form-labelling.html). These field examples (attribution window, value-set OID) deliberately echo that record's own examples.
4. **`.compare`** — a link in the bubble vs a toggletip. Avoid: dark bubble containing text + fake link span (`.tt-link` — inert `<span>`, matching how demos fake `.btn`s): "3 source fields are unmapped. View mapping". Use: same info glyph drawn pressed with an open light `.tt-pop` (paper, border, `--shadow-pop`) holding the sentence + a `--cool` action "View mapping →". Note: "An action means it's a toggletip … whatever the bubble looks like, a button or link inside makes it a popover."

Deliberately cut (do not add): a disabled-control compare pair (survives as reco bullet + §03 principle + precedent card), a separate decision-ladder diagram (§01's table IS the ladder), a standalone icon-naming demo (folded into demo 1).

**§03 The reasoning — `.principles` grid, five cards** (headline + one line): Invisible until asked for · It never takes focus · An ephemeral surface can't carry working content · The behaviour floor is fixed (body = 3-item `ul.clean`: dismissible-Esc / hoverable / persistent — WCAG 1.4.13, not a preference) · Disabled controls swallow events.

**§04 Precedent — `.systems` six cards + `.reco`.** h2 "The systems agree — the bubble stays small and inert". Cards: **IBM · Carbon** (`.syscard.lead`, "Text in a tooltip, actions in a toggletip", verdict `tag v`) · Adobe · Spectrum (160px, a few words, `tag v`) · Atlassian vs Microsoft Fluent 2 (the one real split — disabled controls; we side with Atlassian, `tag h`; mirrors modal-vs-page's disagreement-card structure) · Google · Material 3 (buttons ⇒ `aria-haspopup="dialog"`, `tag h`) · W3C · ARIA APG + WCAG 1.4.13 (the accessibility floor, `tag n`) · Synthesis ("one line · inert · never essential", `tag v`). **Do not cite Material's 200dp/320dp — unverified.** `.reco` "The house rule" = `ul.clean.cool` restating: cap · never sole carrier of essential info · no buttons/links · never on disabled · fixed behaviour (hover AND focus, only on focusable elements, stays while hovered, Esc dismisses) · never restate a visible label, except icon-only buttons where the tooltip IS the label.

**§05 References — `ul.clean.refs.prose`**, real deep links + parenthetical of what each says:
- https://carbondesignsystem.com/components/tooltip/usage/ — IBM Carbon, Tooltip usage (brief, supplementary; icon-button tips one or two words; no interactive content)
- https://carbondesignsystem.com/components/toggletip/usage/ — IBM Carbon, Toggletip usage (the click-triggered sibling that takes focus and may hold interactive content)
- https://spectrum.adobe.com/page/tooltip/ — Adobe Spectrum, Tooltip (160px max-width; a few words or a short sentence); https://spectrum.adobe.com/page/contextual-help/ (the escalation)
- https://atlassian.design/components/tooltip/usage — Atlassian, Tooltip (keep it short; never on disabled elements)
- https://fluent2.microsoft.design/components/web/react/tooltip/usage — Microsoft Fluent 2, Tooltip (the dissent on disabled controls)
- https://m3.material.io/components/tooltips/guidelines — Material 3, Tooltips (rich persistent tooltips with actions wired aria-haspopup="dialog")
- https://www.w3.org/WAI/ARIA/apg/patterns/tooltip/ — ARIA APG, Tooltip pattern ("Tooltip widgets do not receive focus")
- https://www.w3.org/WAI/WCAG21/Understanding/content-on-hover-or-focus.html — WCAG 1.4.13 (dismissible, hoverable, persistent)
- https://www.nngroup.com/articles/tooltip-guidelines/ — NN/g, Tooltip guidelines (non-essential, brief, never redundant with visible text)

## The `.tt-*` primitive in base.css (~40 lines, tokens only, everything in normal flow)

Placed with the other demo kits; comment header stating it's a **schematic demo primitive** (static open bubbles for record demos, not a live component). No `position:absolute`, no transforms, no z-index — `.appframe` has `overflow:hidden`, so every bubble is an in-flow block below its trigger; the caret is an in-flow border-trick element, not a positioned pseudo.

| Class | Purpose / key tokens |
|---|---|
| `.tt-row` / `.tt-anchor` | flex row of demo anchors, wraps narrow; column stack of trigger→caret→bubble (`--space-*` gaps) |
| `.tt-iconbtn` (+`.focused`) | 32px schematic icon button (`--paper`, `--rule-strong`, `--radius-2`); focused = `outline:2px solid var(--cool)` |
| `.tt-help` | 16px "?" circle beside a `.lbl` (mono, `--ink-2`) |
| `.tt-term` | dotted-underline defined term (`border-bottom:1px dotted var(--ink-3)`) |
| `.tt-caret` | in-flow triangle, `border-bottom-color:var(--ink)` |
| `.tt-bubble` (+`.wide`) | THE bubble: `background:var(--ink); color:var(--paper); max-width:240px;` `--radius-2`, `--shadow-pop`, 0.78rem/1.45; `.wide` = 420px anti-pattern |
| `.tt-bubble .hd` / `.tt-bubble ul` | faux heading + bullets for the Avoid bubble only |
| `.tt-kbd` | shortcut chip, mono, translucent paper border, `--radius-1` |
| `.tt-link` | anti-pattern link span: paper-colour, underlined |
| `.tt-pop` (+`.act`) | toggletip surface: `--paper`, `--rule-strong` border, `--radius-3`, `--shadow-pop`, max-width 280px; action in `--cool` |

Reuse existing primitives everywhere else: `.formdemo`/`.fld`/`.lbl`/`.help`/`.inp` (base.css:325–341), `.appframe`/`.appbar`, `.demo`/`.cap`/`.tag`, `.compare`/`.stage`/`.judge`, `.tbl.tri` + `data-l`, `.principles`, `.systems`/`.syscard`/`.verdict`, `.reco`, `ul.clean` (+`.cool`).

## index.html wiring

```html
<a class="regrow" href="records/tooltip-usage.html">
  <span class="status draft"><span class="d"></span>Draft</span>
  <span class="rt">Tooltip usage</span>
  <span class="rd">One short line of non-essential text; anything essential, interactive, or longer moves to helper text or a toggletip.</span>
</a>
```
Append after form-labelling's row (~line 96–100); bump `#components` homenav `.n` 4→5 (line 64). Five rows < the ~7-row split threshold — no `.regsub`.

## Verification

1. Serve locally (`python3 -m http.server` — `file://` is blocked headless) and load the index and the new record.
2. Badge sync: Draft in `.rechead` = Draft in `.regrow`; homenav reads 5.
3. Wide viewport: every bubble fully inside its `.appframe` (nothing clipped); the 420px Avoid bubble visibly dwarfs the 240px Use bubble; tri-table reads.
4. Narrow (~700px): `.compare` stages stack; `.tbl.tri` collapses to labelled rows (every `data-l` present); `.tt-row` wraps; no horizontal overflow.
5. Contract: no `<script>`, no scoped `<style>`, tokens only in the new CSS, no absolute positioning, relative links, British spelling.
