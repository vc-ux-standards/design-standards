# Fix: zero gap above "The house rule" box on the tooltip record

## Context

On `records/tooltip-usage.html`, Section 04 ("Precedent and recommendation"),
the concluding **"The house rule (provisional)"** box sits flush against the
syscards grid above it — zero vertical space, the two borders nearly touch.
It reads as a rendering glitch and breaks the calm rhythm the record otherwise
holds. The box should be separated from the grid the same way it is separated
from a heading on every other record.

## Root cause

The "house rule" box is a `<div class="reco">` (tooltip-usage.html:335), a
sibling that immediately follows the `<div class="systems">` grid (closes at
tooltip-usage.html:333).

In `css/base.css`:

- `.systems` (line 175) has `margin-top:1.5rem` but **no `margin-bottom`**.
- `.reco` (line 184) has **no `margin-top`**.

So nothing separates the grid from the box below it.

This is unique to the tooltip record. In the five other records that use
`.reco` (stepper-orientation, form-layout, form-labelling, breakpoints,
modal-vs-page), `.reco` always follows an `<h2>` or a `<p>`, whose bottom
margin supplies the gap. Only here does `.reco` follow `.systems`.

## The fix — one scoped CSS rule

Add an adjacent-sibling rule to `css/base.css`, next to the existing `.reco`
block (after line 186):

```css
.systems + .reco{ margin-top: var(--space-6); }   /* 1.5rem */
```

Why this selector and value:

- **`.systems + .reco` is surgical.** It matches only when `.reco` directly
  follows `.systems` — which happens on the tooltip record alone. The five
  other records are untouched, so the existing 0.5rem h2→reco gap there does
  not change. A global `.reco{ margin-top }` would wrongly widen that gap
  everywhere via margin-collapse, so it is rejected.
- **`--space-6` (1.5rem)** mirrors `.systems`'s own `margin-top:1.5rem`,
  giving the box symmetric breathing room above and below the grid. The token
  is already defined in `css/tokens.css:43` ("card padding, card→card"), so no
  hard-coded measure and no new token.

No HTML change, no new component, no inline style — a single token-driven line,
consistent with the "reuse tokens, adding a record changes content not CSS"
constraint (this is a bug fix to shared CSS, the sanctioned kind of change).

## Files

- `css/base.css` — add one line after line 186 (`.reco ul.clean{...}`).

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
   `records/breakpoints.html` (§ near line 395) or
   `records/modal-vs-page.html`, and confirm the h2→"house rule" spacing is
   unchanged (still the original tight 0.5rem).
