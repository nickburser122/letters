# جوابات

A single-file Arabic (RTL) app that analyses letter counts by facility, user, center and branch. Everything runs locally in the browser. Data is kept only in localStorage.

## Entry points
- `index.html`: the app
- `smoke-test.html`: a dev harness. It loads the app in an iframe, generates 4,000 rows across 3 branches with near-duplicate names and missing links, then runs the tree, QA, flow and report features. Results go to the console.

## Features
- Paste or upload data (TSV/CSV/XLSX), with automatic cleaning (total rows removed, merged cells filled down, totals reconciled)
- Tabs: Overview, Facilities, Users, Locations, Tree, Flow, Routes, Data quality, Report and export
- **Tree:** only the visible rows are rendered, so there is no row cap. Keyboard: ↑↓ to move, ←→ to open and close (RTL-aware), Home/End, PageUp/PageDown, Enter opens the profile, `*` opens all siblings, `I` isolates. Search matches are highlighted with `<mark>`, and a sticky header shows the level legend and the path of the current row.
- **Saved slices:** the last session (filters, tree order, open nodes, tab, report options) is restored when you reopen the app. You can save named views, for example "Beheira · one center", and reload them with one click.
- **Data quality:** warns about rows missing a user, center or branch, and flags users that appear under several centers and center names used in several branches. Near-duplicate names (facilities and users) are found by fuzzy matching: a deep key that drops the Arabic article and repeated letters, plus Levenshtein distance; names with different digits are never grouped. You can merge a group into a chosen name, mark it "not duplicates", or undo merges. Merges are saved and re-applied on every analysis.
- **Flow:** a Sankey diagram with 4 path presets and 8/12/20/30 nodes per column (the rest go into "أخرى"). Hovering a node traces its chain; clicking it isolates that node across all tabs.
- **Report:** table of contents, a separate report per branch, a page break per branch in the Locations section, landscape pages for the tree and flow (`@page wide`), and an optional flow-diagram section
- **Excel:** the tree is exported as an indented sheet with collapsible row groups (outline levels, collapsed below level 2), following the current tree order and filters

## Storage keys
`jawabat.theme`, `jawabat.session`, `jawabat.data`, `jawabat.views`, `jawabat.merges`, `jawabat.dupign`, `marsad.cls`

## Remaining / to verify
- Manual browser check of printing (landscape named pages, TOC links in PDF) and of how outline groups look when the file is opened in Excel
- Fuzzy grouping compares names within 3-letter prefix and suffix buckets and skips buckets larger than 500 names
