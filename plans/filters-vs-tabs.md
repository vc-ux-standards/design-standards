# Plan: Tabs are not filters — a new decision record

## Checklist

- [ ] 1. Research precedent against live docs — Carbon (Tabs usage + data-table Filtering pattern), Material 3 (Tabs + filter chips), Polaris (IndexFilters — renders saved filter sets *as* tabs; the one real complication), Atlassian (Tabs), NN/g ("Tabs, Used Right" + faceted filtering), Spectrum (Tabs, optional sixth card). Verified quotes only; anything unverifiable is cut, not paraphrased.
- [ ] 2. Create `records/filters-vs-tabs.html` from `records/_template.html` — full record per "The record" below (masthead, §01 table, §02 two compare pairs, §03 principles, §04 edge cases, §05 systems, §06 recommendation, §07 references)
- [ ] 3. Wire `index.html`: append the `.regrow` as the sixth row under `#components` (after tooltip-usage, ~line 105), bump the Components homenav count 5→6 (line 64)
- [ ] 4. Verification pass: serve locally (`python3 -m http.server`), wide + narrow viewports, badges in sync, `data-l` on every `<td>`, no `<script>`/`<style>`, relative links, British spelling

## Context

VC's registries and worklists narrow data constantly, and tabs and filters both cut a screen down — that surface similarity is the trap. The standing error is a tab bar wearing a column's values: one tab per status, with an "All" tab as the confession. No record covers this; `stepper-orientation.html:256` already says "Peer sections with no required order are tabs, not a stepper" — this record formalises the other half and cross-links back.

## Decisions locked with the user

- **Title: "Tabs are not filters"** (h1, no `<br>` needed); slug `filters-vs-tabs`; index `.rt` "Tabs are not filters".
- **Status Draft** in both `.rechead` and `.regrow`; rulecard label "The rule (provisional)".
- **Eyebrow "Components · Navigation"** → `../index.html#components` (topnav and rechead both). Components stays a flat 6-row list — a Navigation family earns a `.regsub` only at 2 records.
- **Scope tabs are a sanctioned exception**: at most one coarse scope strip (2–3 mutually exclusive populations, e.g. "My panel / All members"), placed above the filter bar; a scope switch never silently clears filter state; anything mirroring a finer column is a filter.
- **Precedent is researched and filled**, not stubbed — §05 with real syscards, §07 References with deep links.
- **Segmented controls / view toggles** (table ↔ chart of the same rows) explicitly deferred: one edge-case bullet — "a presentation switch is neither a tab set nor a filter, and it must never reset filters" — full guidance left to a future record.
- **AI filters** named once, capability only: "AI filters produce ordinary filter chips — visible, editable, removable like any hand-built filter." Never internals. (Naming precedent: modal-vs-page already says "the AI-filter registry builder".)
- **No new CSS.** Pseudo-tab strips and filter chips are schematic `.btnrow`/`.btn` spans — demos are schematic, never the real product. If a second record ever needs a real tab primitive, that is the sanctioned `base.css` conversation.

## The record

**Register model:** `modal-vs-page.html` (rulecard as intro + `ul.clean` + threshold sentence; compare pairs) with `stepper-orientation.html` supplying the `.tbl` / `.principles` / `.systems` / `.reco` shapes. Match their voice exactly: declarative, British spelling, every block leads with a bolded claim.

**The house rule — match the control to what changes:**
- **Tabs** — a small fixed set (2–7, chosen at design time) of mutually exclusive peer views that differ **structurally**: own layout, columns, actions. One click swaps the screen; position is transient.
- **Filters** — narrowing **one dataset** by its columns: many-valued, combinable across dimensions (status AND programme AND payer), saveable as a named view. Same table, fewer rows.
- **The tell:** options that mirror a column's values are filter values, not tabs. If two tabs would render the same columns, they are one view with a filter. An "All" tab is the confession.
- **Coexistence:** tabs own structure, filters own rows; a tab switch never silently clears filter state; a shared filter bar carries only filters that apply to every tab.

**The deciding question (one line):** does switching change **what the screen is**, or only **which rows it shows**? Structure → tabs. Rows → filters.

### Masthead

- **`.lead`** (~3 sentences, verdict-first): Tabs and filters both cut a screen down, and that surface similarity is the trap. Tabs switch between a small fixed set of structurally different peer views; filters narrow one dataset into subsets an analyst can combine, save and share. The standing error is a tab bar wearing a column's values — one tab per status.
- **`.context`** (~2 sentences, bolded constraint): analysts and care managers working dense registries and worklists on large monitors. What decides it: our data is **many-valued and multi-dimensional — status, programme, payer, risk tier — and the subsets analysts cut get saved and shared as views**. Filters are the default for narrowing; tabs are reserved for views that genuinely differ in structure.
- **`.rulecard`** ("The rule (provisional)", modal-vs-page shape): intro line, `ul.clean` with the two bolded definitions (Tabs / Filters as above), threshold paragraph carrying the deciding question + the tell.

### Sections (7)

**§01 Quick reference — "Decide by what changes on click."** 2-column `.tbl` (Tabs / Filters), `data-l` on every `<td>`. Rows:
1. **Number of options** — Tabs: 2–7, fixed at design time. Filters: unbounded; values come from the data.
2. **Combinability** — Tabs: one at a time, mutually exclusive. Filters: compose across dimensions.
3. **Structural difference** — Tabs: each view has its own columns, layout, actions. Filters: same table, fewer rows.
4. **What a click changes** — Tabs: swaps the screen. Filters: narrows rows in place.
5. **State** — Tabs: transient position, not worth saving. Filters: an artifact — saved views, shared URLs.
6. **Counts** — Tabs: a badge per tab. Filters: result count line ("1,204 of 18,500 members") and facet counts.
7. **Best fit in our app** — Tabs: facets of one record (a measure's Definition / Population / Results). Filters: registries, worklists, member and cohort tables.

**§02 The two in practice — "The error runs both directions."** Two `.compare` pairs:
- **Pair A — tabs used as filters** (headline anti-pattern). Avoid: `.appframe` "Member registry", pseudo-tab strip "Active / Inactive / Pending / Expired / All" (`.btnrow` + `.btn g` spans, one `.btn p` selected) over a `.faux-list` with `.pill` statuses. Note: "**One tab per status value — a column promoted to chrome.** No way to combine status with programme or payer, and the 'All' tab is the confession." Use: same frame, filter chips ("Status: Active ×", "Programme: MSSP ×", "+ Filter"), count line "1,204 of 18,500 members", same list. Note: "**One table, one filter bar.** Status combines with any column, and the set saves as a view."
- **Pair B — filters used where views differ structurally.** Avoid: `.appframe` "Quality measure: 30-day readmission" with a chip "Section: Results ×" over one lowest-common-denominator list where half the cells read "—". Note: "**A 'Section' filter pretending three different screens are one dataset** — the columns only fit one of them." Use: pseudo-tab strip "Definition · Population · Results" (Results selected), canvas showing genuinely different structure (a `.fieldgrid` or small chart). Note: "**Three peer views of one measure, each with its own layout and actions.** A structural switch is exactly what tabs are for."

**§03 The reasoning — `.principles` grid, five cards** (headline + one line):
1. **Filter values come from the data** — a tab set is fixed at design time; status, programme and payer values grow with configuration, and chrome that mirrors a column cannot keep up.
2. **Narrowing is multi-dimensional** — registry questions are conjunctions (status AND programme AND risk tier); tabs give one dimension, exclusively.
3. **Filter state is an artifact; tab state is position** — analysts save, share and revisit a filter set as a view; nobody saves which tab they were on. If the selection deserves a name, it is a filter.
4. **Tabs promise a structural change** — if the same table comes back with fewer rows, the promise is broken.
5. **Two systems must not fight** — tabs own structure, filters own rows, and a tab switch never silently discards filter state.

**§04 Edge cases and anti-patterns — "Most of this rule's work is policing the boundary."** `ul.clean.cool` (sanctioned) / `ul.clean.anti` (reject in review):
- Sanctioned: the scope-tab exception (as locked above) · a saved-view strip is a filter feature, not a tab set — it holds named filter states, grows at runtime, and each entry opens the same table (the Polaris IndexFilters pattern, cited in §05) · AI filters bullet · the segmented-control deferral bullet.
- Reject: tab-per-column-value · an "All" tab · a "Section" filter across structurally different views · a tab switch that silently clears filters · ordered steps as tabs — "**Ordered steps are a stepper, not tabs**", linking `stepper-orientation.html` (reciprocal of its line 256).

**§05 What the major design systems say — researched, verified quotes only.** `.systems` grid, `.syscard.lead` for Carbon: Carbon (Tabs = related content at the same hierarchy level; data-table Filtering pattern with applied-filter tags) · Material 3 (Tabs = navigation between peer groups, not actions; filter chips for narrowing) · **Polaris — the complication card** (IndexFilters deliberately renders saved filter sets as tabs; frame it as a saved-view strip wearing tab clothes, consistent with our §04 position — mirrors modal-vs-page's disagreement-card structure) · Atlassian (Tabs for different views or functional aspects) · NN/g ("Tabs, Used Right": alternate views of similar content, no cross-tab comparison) · Spectrum optional. Every claim verified against the live page before it lands; unverifiable → cut.

**§06 Recommendation for VC — `.reco` + `ul.clean.cool`:** filters default for registries, worklists, member and cohort tables · tabs reserved for 2–7 structurally different facets of one record · no tab-per-column-value, ever · one coarse scope strip sanctioned, above the filter bar, never clearing filter state · AI filters produce ordinary chips · presentation toggles deferred.

**§07 References — `ul.clean.refs.prose`**, deep links + one-line parentheticals, filled during the research pass (Carbon tabs + filtering pattern, Material 3 tabs + chips, Polaris IndexFilters, Atlassian tabs, NN/g Tabs Used Right + filtering articles, Spectrum tabs if cited).

## index.html wiring

```html
<a class="regrow" href="records/filters-vs-tabs.html">
  <span class="status draft"><span class="d"></span>Draft</span>
  <span class="rt">Tabs are not filters</span>
  <span class="rd">Tabs switch structurally different peer views; filters narrow one dataset. Options that mirror a column's values are filters, never tabs.</span>
</a>
```
Append after tooltip-usage's row (~line 105, before `</section>`); bump `#components` homenav `.n` 5→6 (line 64). Six rows < the ~7-row split threshold — no `.regsub`.

## Open items to verify during execution

1. **Does VC ship a tabs component today?** The 2–7 cap and count-badge row must match the live component library (the site's own footer: "verify against the live component library before build").
2. **Segmented-control deferral** — record the intended future slug (suggest `view-toggles.html`) or leave unnamed.

## Verification

1. Serve locally (`file://` is blocked headless) and click through from the index.
2. Badge sync: Draft in `.rechead` = Draft in `.regrow`; homenav Components reads 6.
3. Wide viewport: both compare pairs side by side; the Avoid tab strip visibly reads as a status list promoted to chrome.
4. Narrow (~700px): `.compare` stages stack; `.tbl` collapses to labelled rows (every `data-l` present); no horizontal overflow.
5. Contract: no `<script>`, no `<style>`, no new CSS, tokens only, relative links, British spelling, "VC" only (pre-commit hook enforces).
