# Design Decisions — VC

The design decision record and standards reference for VC. Plain HTML and CSS, no JavaScript, no build step, no framework — deliberately. The site is 10–20 pages, optimised for durability and zero maintenance. It works opened straight from disk (`index.html`) and published on GitHub Pages.

The visual language (OKLCH paper/ink palette, Source Serif 4 / Hanken Grotesk / IBM Plex Mono, 64ch measure) is canonical and lives in the two stylesheets. Do not redesign it.

## Repo structure

```
design-standards/
  index.html                  ← pattern index, grouped by category; hand-maintained
  records/
    _template.html            ← blank record page with TODO comments — duplicate this
    stepper-orientation.html  ← Stepper orientation (Adopted)
    modal-vs-page.html        ← Modal, full page, or side panel (Draft)
  css/
    tokens.css                ← :root custom properties (palette, measure, gutter)
    base.css                  ← typography, prose, layout, page shell, demo-frame styles
  README.md
```

All links are relative (`../css/base.css`, never `/css/base.css`) so the site works both from disk and from a GitHub Pages project path like `/design-standards/`. Keep it that way.

## How to add a page

1. Duplicate `records/_template.html` and name it after the record slug, e.g. `records/form-layout.html`.
2. Fill in every `<!-- TODO -->`: title, category eyebrow, status badge, the numbered sections, demos.
3. Add one `<a class="dircard">` entry for it in `index.html`, under the right category section (add a new section if the category is new).

That is the entire workflow. No build step, no registration, nothing to generate.

## Status lifecycle

Each record carries a status badge in its masthead (`.rechead`) and on its index card. Keep the two in sync.

| Status | Badge class | Meaning |
| --- | --- | --- |
| Draft | `.status.draft` | Being written; argue with it, don't cite it. |
| Adopted | `.status.adopted` | The house rule; cite it in review. |
| Superseded | `.status.superseded` | Replaced by a newer record; link the replacement. |

## Publishing on GitHub Pages

1. Push the repo to GitHub.
2. In the repo: **Settings → Pages → Build and deployment**, set Source to **Deploy from a branch**, branch `main`, folder `/ (root)`.
3. Save. The site appears at `https://<org>.github.io/<repo-name>/`. No Actions workflow is needed.

## Conventions

- Pattern-level content only: layout rules, labelling rules, generic demos. No product-specific screens or data.
- No JavaScript. The only permitted exception is a small inline script scoped to a single demo that genuinely needs it to demonstrate an interaction (none so far).
- No inline `<style>` blocks; pages link `css/tokens.css` and `css/base.css`. A single demo may carry scoped styles if it genuinely needs them.
- Good/bad comparisons use the `.compare` block (`.stage.avoid` / `.stage.use`): side by side on desktop, stacked on narrow viewports.
