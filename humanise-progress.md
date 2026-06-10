# Humanise copy — progress

Two-pass copy improvement (scannability → humanise wiring), one page per run,
reviewed against `records/stepper-orientation.html` before committing.

Run with: `/humanise <page-path>`

## Order (lowest-risk first, so the prompt is calibrated before the meatier pages)

- [ ] `records/form-labelling.html`
- [ ] `records/form-layout.html`
- [ ] `records/modal-vs-page.html`
- [ ] `design-decisions.html` — longest; do last, once the prompt is tuned
- [ ] `index.html` — dircard blurbs only; tiny, do whenever

## Do not touch

- `records/stepper-orientation.html` — the calibration anchor (already in register)
- `records/_template.html` — TODO scaffolding, not prose

## Per-page checklist

1. Run `/humanise <page>`.
2. Diff side by side with `stepper-orientation.html` — could a reader tell which
   one was touched? It should be indistinguishable in register.
3. If chattier / hedgier / flatter in rhythm: tighten the prompt, re-run.
4. Approve → commit (one commit per page) → tick the box above → next page.
