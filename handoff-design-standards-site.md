# Handoff: Build the design-standards static site

**Date:** 2026-06-10
**Project:** design-standards (new repo)
**For:** A fresh Claude Code session. This document is the complete brief — no prior conversation context needed.

## What this is

A plain HTML + CSS static site that serves as the design decision record and standards reference for an enterprise healthcare analytics product. It will be published on GitHub Pages and must also work by opening `index.html` directly from disk.

The seed already exists: `design-decisions.html` (in the repo root, I will place it there before you start). It is a polished, self-contained page with a complete visual language. The whole site must look like that file.

## Decisions already made — do not revisit

- **No JavaScript, no build step, no framework.** Plain HTML and CSS only. This was a deliberate choice over Astro/Next.js: the site is 10–20 pages, optimised for durability and zero maintenance, not scale. Do not introduce npm, bundlers, generators, or client-side JS. The only exception: if a specific demo genuinely needs scoped JS to demonstrate an interaction, it may be inlined in that demo only.
- **Templating is replaced by a page shell.** Since there are no includes, every page copy-pastes one canonical skeleton. Adding a page = duplicate the template, fill it in, add one line to the index. Anything more ceremonial than that kills the site.
- **The visual language in `design-decisions.html` is canonical.** OKLCH custom properties, Source Serif 4 / Hanken Grotesk / IBM Plex Mono (Google Fonts), 64ch measure, paper/ink palette. Extract it; do not redesign it.
- **Pattern-level content only.** Layout rules, labelling rules, generic demos. No product-specific screens or data.
- **Relative paths everywhere.** GitHub Pages project sites serve from `/repo-name/`, so absolute paths break. `../css/base.css`, never `/css/base.css`.

## Build spec

### Repo structure

```
design-standards/
  index.html              ← pattern index, grouped by category
  records/
    _template.html          ← blank record page with TODO comments
    stepper-orientation.html ← ported record (status: Adopted)
    modal-vs-page.html       ← ported record (status: Draft)
  css/
    tokens.css            ← :root custom properties, extracted
    base.css              ← typography, prose, layout, demo-frame styles
  design-decisions.html   ← source file (leave in place until port is verified)
  README.md
```

### 1. Extract the CSS

- `css/tokens.css`: the `:root` block from `design-decisions.html`, verbatim.
- `css/base.css`: its typography, prose, layout (`.wrap`, `.prose`, measure), eyebrow/heading styles, and demo-frame styles.
- Add to `base.css` a shared two-column good/bad demo class: side-by-side stages labelled **Avoid** / **Use**, stacking to one column on narrow viewports.
- Pages link both stylesheets. No inline `<style>` blocks except where a single demo genuinely needs scoped CSS.

### 2. The page shell

Identical markup on every page:

- `<head>`: charset, viewport, title (`Record name · Design Decisions`), Google Fonts preconnect + stylesheet, both CSS files via relative paths.
- Top nav: site title linking to the index, plus the page's category eyebrow (e.g. "Component usage · Steppers").
- The source file already has a record masthead pattern: `.rechead` with a status badge (`.status.adopted` / `.status.draft`) plus eyebrow, then the `<h1>`. **Reuse that exact markup and CSS as the page header — do not invent a new metadata strip.** Extend the status set to Draft / Adopted / Superseded in CSS (Superseded styling: muted, like Draft but grey).
- Content area constrained to the measure; demos may break out wider.
- Footer: reuse the existing `.globalfoot` ("Value Connect · UX decision records · Internal · Verify against the live component library before build.").

### 3. `records/_template.html`

The source file already contains add-a-record instructions in an HTML comment near the `modal-vs-page` record ("Copy one `<article class="record" id="slug">` block... That is the only wiring. No build step."). Honour that intent in multi-page form: `_template.html` is a blank record page using the shell, with `<!-- TODO -->` comments at every fill-in point (title, eyebrow category, status, sections using the `.secnum` numbering pattern, demo blocks). Carry the original comment's spirit forward: duplicating this file plus one index entry is the entire workflow.

### 4. `index.html`

- The source file has a `#home` directory section with `.dircard` link cards and a sidebar nav. **Port that directory as the index page** — same dircard markup and styles, grouped by category eyebrow (currently: Component usage, Layout; more categories will come, e.g. Standards · Forms).
- Each card: record title, one-line summary, status badge.
- Hand-maintained. HTML comment at the top: to add a record, duplicate `records/_template.html`, fill it in, add one dircard here.

### 5. Port `design-decisions.html`

- The file contains exactly two records: `#stepper-orientation` (vertical vs horizontal stepper, status Adopted, category "Component usage · Steppers") and `#modal-vs-page` (modal vs full page vs side panel, status Draft, category "Layout · Containers"). Each becomes one page under `records/`, keeping its slug as the filename.
- Preserve all content **exactly as-is** — including the sourced precedent quotes in the stepper record and the explicit "To be completed" precedent section in the modal record (that stub is intentional; do not fill it in).
- Internal anchors that pointed to `#slug` within the single page become relative links between pages.
- Keep the source file in the repo root until I verify the port, then I will delete it.

### 6. README

Cover: repo structure, how to add a page (the duplicate-template workflow), the status lifecycle (Draft → Adopted → Superseded, matching the existing badges), and how to enable GitHub Pages (Settings → Pages → deploy from `main`, root; no Actions needed).

## Acceptance checks

- [ ] Opening `index.html` from disk renders correctly with working links and styles.
- [ ] Every page in `records/` uses the identical shell and links resolve relatively.
- [ ] No JavaScript anywhere except demo-scoped exceptions (expect zero in v1).
- [ ] No build tooling: no `package.json`, no config files beyond `.gitignore`.
- [ ] The site is visually indistinguishable in language from `design-decisions.html` (fonts, palette, measure, spacing).
- [ ] The two ported records read identically to their versions in the source file, including the intentional "To be completed" stub.
- [ ] `_template.html` can be duplicated and filled in under five minutes.
- [ ] Good/bad demos sit side by side on desktop, stack on mobile.

## Next steps after the build (for me, not Claude Code)

1. [ ] Verify the port page-by-page against `design-decisions.html`, then delete the source file from the repo.
2. [ ] Push to GitHub, enable Pages, confirm the published URL renders with relative paths intact.
3. [ ] Add the form design standard content (layout stacking + label/hint/placeholder rules) as it is produced — this repo is where that artifact now lives.
4. [ ] Once stable, share the published URL with the design team alongside the Excel UX-requirements template.
