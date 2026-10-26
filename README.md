# جوابات

A single-file Arabic (RTL) app that analyses letter counts by facility, user, center and branch. Everything runs locally in the browser. Data is kept only in localStorage.

## Entry points
- `index.html`: the app
- `smoke-test.html`: a dev harness. It loads `index.html?test` in an iframe, generates 4,000 rows across 3 branches with near-duplicate names and missing links, runs the tree, QA, flow and report features, then runs assertions that log `PASS name` / `FAIL name: detail` and a final `RESULT` line. Serve the folder over http (for example `python3 -m http.server`) and open `smoke-test.html` with the console open; a `file://` page cannot reach into the iframe.

## Features
- Paste or upload data (TSV/CSV/XLSX), with automatic cleaning (total rows removed, merged cells filled down, totals reconciled)
- Tabs: Overview, Facilities, Users, Locations, Tree, Flow, Routes, Data quality, Report and export
- **Tree:** only the visible rows are rendered, so there is no row cap. Keyboard: ↑↓ to move, ←→ to open and close (RTL-aware), Home/End, PageUp/PageDown, Enter opens the profile, `*` opens all siblings, `I` isolates. Search matches are highlighted with `<mark>`, and a sticky header shows the level legend and the path of the current row.
- **Saved slices:** the last session (filters, tree order, open nodes, tab, report options) is restored when you reopen the app. You can save named views, for example "Beheira · one center", and reload them with one click.
- **Data quality:** warns about rows missing a user, center or branch, and flags users that appear under several centers and center names used in several branches. Near-duplicate names (facilities and users) are found by fuzzy matching: a deep key that drops the Arabic article and repeated letters, plus Levenshtein distance; names with different digits are never grouped. You can merge a group into a chosen name, mark it "not duplicates", or undo merges. Merges are saved and re-applied on every analysis.
- **Flow:** a Sankey diagram with 4 path presets and 8/12/20/30 nodes per column (the rest go into "أخرى"). Hovering a node traces its chain; clicking it isolates that node across all tabs.
- **Report:** table of contents, a separate report per branch, a page break per branch in the Locations section, landscape pages for the tree and flow (`@page wide`), and an optional flow-diagram section
- **Excel:** the tree is exported as an indented sheet with collapsible row groups (outline levels, collapsed below level 2), following the current tree order and filters

## Copy and export
- **Table registry:** every copyable dataset is one entry in `TABLES` (`cl`, `pe`, `loc`, `pair`, `tree`, `treei`, `cls`, `raw`, `kpi`, `ins`, `mixc`, `mixt`, `flow`, `qaMiss`, `qaMultiU`, `qaMultiC`, `qaDup`). The same rows feed the screen, the Excel/CSV export and the copy output. `visibleList`, `visibleLocs` and `visibleRoutes` apply the search box, sort and the "ناقصة التصنيف" toggle for both the renderer and the copy output.
- **Report export card:** the copy button now copies every checked dataset, stacked and separated by one empty line (it used to copy only the first).
- **Formats** (`serialize(items, fmt, opts)`):
  - TSV: `الجهة<TAB>العدد<TAB>الحصة %` then `معمل البرج<TAB>980<TAB>9.9%`. The clipboard also receives an HTML `<table dir="rtl">` so Word, Outlook and Gmail paste a real table.
  - Markdown: `### الجهات`, a `|`-table with `---:` for numeric columns, `\|` for a pipe inside a cell; the tree is a nested list.
  - Text: numbered lines such as `1. معمل البرج — 980 (9.9%) · خاص`, with no padded tables.
  - CSV: same rows as TSV with quoting.
- Empty values are always the empty string in copy and export; `—` is for the screen only.
- If the clipboard is blocked, a dialog opens with the text selected so Ctrl+C works.
- `?test` on the URL exposes `window.__jw` (registry, serializers, visible-row helpers, state) for the smoke test.

## Copy from where you are
- Every tab has a split button in its toolbar: **نسخ** copies the tab's visible rows (search, sort and filters applied) in the last-used format; **▾** opens the copy panel. Shortcut: `Ctrl/⌘+Shift+C` copies the current tab.
- **Copy panel:** format (TSV, Markdown, نص, CSV), dataset checkboxes preset by the tab, options (header row, title/scope/date line, thousands separators, percent style, row limit, totals row, WhatsApp bold, one block per branch, tree depth), a live preview of the first 300 lines with a row/column/character counter, copy and download. Options are saved in `jawabat.copy`. Esc closes it. If the browser blocks the clipboard the same panel opens with the text selected.
- **Row selection** in Facilities, Users, Locations and Routes: Ctrl/⌘-click toggles a row, Shift-click selects a range, Esc clears. The button becomes `نسخ المحدد (n)` and Ctrl/⌘+C copies the selected rows as TSV with the header.
- **Drawer:** a `نسخ` button next to Excel and CSV copies the profile (header block plus tables).
- **Report tab:** the export card has a format selector, `TXT` and `MD` downloads, and the report card has `نسخ التقرير كـ Markdown` and `نسخ التقرير كنص` (sections in the report order, limited by the rows setting, one section per branch when that switch is on).

## Analysis additions
- **جدول محوري (pivot):** a tab after المسارات. Pick the row and column dimension (جهة, مستخدم, مركز, فرع, تصنيف, نوع; the ones missing from your data are hidden), the value (count, % of row, % of column, % of total), the number of top columns (5, 10, 20 or all; the rest become `أخرى`) and whether to show totals. It is a plain table, registered as `TABLES.pivot`, so TSV, Markdown, text and CSV copying work. Example TSV: `الجهة<TAB>فرع البحيرة<TAB>فرع القاهرة<TAB>الإجمالي`.
- **Top N per group:** in المواقع and المستخدمون, `أعلى [5] جهات لكل [مركز/مستخدم/فرع]` with `عرض` and `نسخ`. Columns: group, rank, facility, count, % of group. Registered as `TABLES.topn` and offered in the copy panel.
- **مقارنة (period comparison):** paste an earlier period in the optional `بيانات الفترة السابقة` box on the input screen. A `مقارنة` tab appears with one table per level (facility, user, center): previous, current, change, % change and a status of new (`جديد`), disappeared (`اختفى`), increase, decrease or unchanged. It uses the same cleaning, the same saved name merges and the current filters. Registered as `TABLES.cmpfac`, `TABLES.cmpuser` and `TABLES.cmpcenter`.

## Storage keys
`jawabat.theme`, `jawabat.session`, `jawabat.data` (now also keeps the previous-period text), `jawabat.views`, `jawabat.merges`, `jawabat.dupign`, `marsad.cls`, `jawabat.copy` (last-used copy format and options)

## Remaining / to verify
- Manual browser check of printing (landscape named pages, TOC links in PDF) and of how outline groups look when the file is opened in Excel
- Fuzzy grouping compares names within 3-letter prefix and suffix buckets and skips buckets larger than 500 names
