# Fix: zero gap above "The house rule" box on the tooltip record

## Context

On `records/tooltip-usage.html`, Section 04 ("Precedent and recommendation"),
the concluding **"The house rule (provisional)"** box sits flush against the
syscards grid above it — zero vertical space, the two borders nearly touch.
It reads as a rendering glitch and breaks the calm rhythm the record otherwise
holds. The box should be separated from the grid the same way it is separated
from a heading on every other record.

## Root cause

The "house rule" box is a `<div class="reco">` on the tooltip record, a sibling
that immediately follows the `<div class="systems">` grid.

In `css/base.css`:

- `.systems` has `margin-top:1.5rem` but **no `margin-bottom`**.
- `.reco` has **no `margin-top`**.

So nothing separates the grid from the box below it.

**Today** only the tooltip record has this adjacency. The five other records
that use `.reco` (stepper-orientation, form-layout, form-labelling,
breakpoints, modal-vs-page) put reco in a **separate section** after an
`<h2>` or a `<p>`, whose bottom margin supplies the gap.

That is not a permanent one-off. `_template.html` already tells authors to put
systems **then** reco in one "Precedent and recommendation" section. Future
records from the template will hit the same flush gap. This is a **missing
shared spacing rule for the co-located pattern**, not a tooltip-only band-aid.

## The fix — one scoped CSS rule

Add an adjacent-sibling rule to `css/base.css` under the existing **Spacing
rhythm** block (the home for other `A + B` rules such as
`:is(.demo, .compare) + :is(.demo, .compare)`), not next to the `.reco`
component styles:

```css
.systems + .reco{ margin-top: var(--space-6); }   /* 1.5rem · card→card */
```

Why this selector, value, and placement:

- **`.systems + .reco` is surgical.** It matches only when `.reco` directly
  follows `.systems`. Records that put reco after an `h2`/`p` are untouched,
  so the existing 0.5rem h2→reco gap there does not change. A global
  `.reco{ margin-top }` would wrongly widen that gap everywhere via
  margin-collapse, so it is rejected.
- **`--space-6` (1.5rem)** mirrors `.systems`'s own top margin, giving the box
  symmetric breathing room above and below the grid. The token is already in
  `css/tokens.css` ("card padding, card→card") — no hard-coded measure, no new
  token.
- **Spacing rhythm block** is the established home for contextual adjacent-
  sibling air. Putting the rule next to `.reco`'s component block would work
  but ignore the house convention.

No HTML change, no new component, no inline style — a single token-driven line.
Shared-CSS bug fix of the sanctioned kind (template co-location needs this
rule; content pages stay as they are).

## Files

- `css/base.css` — one line under the Spacing rhythm block (near the other
  `+` sibling rules).

## Verification

No tests / no CI — visual inspection from disk per project convention.

1. Serve the folder (headless `file://` is blocked):
   `python3 -m http.server` in the repo root, open
   `http://localhost:8000/records/tooltip-usage.html`.
2. Scroll to Section 04. Confirm the "The house rule (provisional)" box now
   has a clear ~1.5rem gap above it, matching the space between the intro
   paragraph and the top of the syscards grid.
3. **Narrow viewport** (≤760px, where `.systems` collapses to one column):
   confirm the gap holds and the box still reads as separated from the last
   card.
4. **Regression check** — open one other `.reco` record, e.g.
   `records/breakpoints.html` or `records/modal-vs-page.html`, and confirm
   the h2→"house rule" spacing is unchanged (still the original tight 0.5rem).
