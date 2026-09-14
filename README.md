# Olympic Production Planner

A single-page, no-backend dashboard that turns an Olympic Paints **Batch Transfer** PDF (the Odoo `BATCH/OUT` export) into a sorted, department-split **Production Requirement List** — on screen and as a print-ready PDF.

Everything runs client-side in the browser: the PDF is parsed, verified and aggregated locally, and nothing is ever uploaded to a server.

> Build: v1.0 — 2026‑09‑14

---

## What it does

1. **Upload** one or more "Batch Transfer" PDFs (drag-and-drop or browse). You can drop several exports at once if a day's dispatch spans more than one file.
2. **Extracts** every outbound product line from the PDF using [pdf.js](https://mozilla.github.io/pdf.js/) — no OCR, this only works on PDFs with real selectable text (which is what Odoo exports).
3. **Verifies** every batch is a genuine outbound stock movement (`FROM Stock` → `TO Customers`, transfer type `OP/OUT`) and flags anything that can't be confirmed.
4. **Aggregates** line items by product + pack size, combining quantities from multiple batches into an addition expression (e.g. `12+23=35`) and listing the contributing batch numbers together (e.g. `#00171 / #00173`).
5. **Splits** the result into two departments:
   - **Putty Department** — Lacquer Thinners, Carbolineum, Stainers, Putty (all variants), Crack Fillers, Turpentine, Oxide (every colour found), Spirit Of Salts, and Distemper (every colour found). Each size stays on its own row — nothing is summed into a single total.
   - **Paint Department** — everything else. Nothing from Putty Department is duplicated here.
6. **Sorts** everything alphabetically by product, then by pack size in the order `20L → 5L → 1L → 500ML → other`.
7. **Renders** a dashboard: total quantity dispatched for 20L / 5L / 1L / 500ML, a prominent dispatch-date banner, an outbound-verification badge, and both department tables.
8. **Generates a PDF** in one click, matching the production team's existing report format: logo, title, dispatch date up top, totals summary, the two department tables (quantities bold), and the source batch numbers + verification note at the very end.

## Why this shape

The report intentionally leaves the date **off** every line item — production doesn't need it row by row — but puts the truck's dispatch date front and center near the top so it's visible at a glance. The batch numbers and the outbound-verification note move to the very end, since they're a paper trail for the office, not something production needs to read first.

## Using it

Just open `index.html` in a browser — locally, from GitHub Pages, or from any static host. There's no build step and no server.

1. Drop your Batch Transfer PDF(s) onto the upload card, or click to browse.
2. Click **Generate Production List**.
3. Review the dashboard — check the verification badge, totals, and both department tables.
4. Click **Download Production Requirement List (PDF)** to get the print-ready report.

If a batch can't be automatically confirmed as outbound, or a category you'd expect (like Carbolineum) has zero items that day, the tool says so explicitly in the verification badge and in a footer note — it never silently hides a gap or fabricates a zero row.

## Deploying it

**GitHub Pages**
1. Push `index.html` and `README.md` to a new repository.
2. In the repo, go to *Settings → Pages*, set the source branch to `main` and the folder to `/ (root)`.
3. Your dashboard will be live at `https://<your-username>.github.io/<repo-name>/`.

**Vercel**
1. Import the repository at [vercel.com/new](https://vercel.com/new).
2. Leave the framework preset as "Other" — no build command is needed, it's a static file.
3. Deploy.

## Customizing

Everything lives in one file, `index.html`, organized into numbered sections in the inline `<script>`:

| Section | What to edit for... |
|---|---|
| `2. PDF TEXT EXTRACTION` | How PDF pages are turned into lines of text |
| `3. LINE PARSER` | The regular expressions that recognize batch headers, product lines, and FROM/TO transfer lines — update these if Odoo's export format changes |
| `5. DEPARTMENT SPLIT` | The product-name rules that decide Putty Department vs Paint Department — add a new `staticCategories` entry here to move another product family into Putty |
| `10. PDF REPORT EXPORT` | The layout, fonts, and colors of the downloadable PDF |

The Olympic Paints logo is embedded directly as a base64 data URI (`LOGO_DATA_URI`) near the top of the script — replace that string to swap the logo.

## Tech stack

- No framework, no build step — a single static HTML file.
- [pdf.js 3.11.174](https://mozilla.github.io/pdf.js/) for in-browser PDF text extraction.
- [jsPDF 2.5.1](https://github.com/parallax/jsPDF) + [jsPDF-AutoTable 3.8.2](https://github.com/simonbengtsson/jsPDF-AutoTable) for generating the downloadable report.
- Vanilla JS, CSS custom properties for the light/dark theme (defaults to dark).

## Limitations

- Only works on PDFs with real, selectable text (Odoo's native export). A scanned or photographed batch transfer sheet won't parse — the tool will tell you so rather than fail silently.
- The Putty/Paint category rules are tuned to Olympic Paints' current product naming. If a new product line is introduced with different naming conventions, extend the rules in section 5 of the script.

---

*Built for Olympic Paints production & dispatch teams.*
