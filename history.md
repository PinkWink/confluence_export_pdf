# Confluence PDF Exporter — Project History

> User requests & Claude implementation log

---

## 1. Confluence PDF Quality Improvement Request — *User*

The built-in Confluence Cloud PDF exporter produces low-quality output, especially lacking line numbers in code blocks. The user requested a solution using MCP or API to convert Confluence pages to HTML/PDF.

- Domain: `https://pinkwink.atlassian.net/`
- Email: `pinkwink.korea@gmail.com`
- API Token: Provided
- Output format: Both HTML and PDF

## 2. Approach Design & Planning — *Claude* `Plan`

Evaluated three approaches (Confluence REST API + script, Confluence MCP Server, WebFetch) and chose **REST API + Python script**.

- Created conda environment `confluence` (Python 3.11)
- Packages: `requests`, `beautifulsoup4`, `playwright`, `python-dotenv`
- PDF rendering via Playwright Chromium

## 3. Core Script Implementation — *Claude* `Done`

Wrote `confluence_export.py` with the following features:

- Automatic switching between Confluence REST API v2/v1
- Auto-extraction of page ID from URL
- Code block line numbers (`<ac:structured-macro>` → styled HTML)
- Image base64 inline embedding
- Confluence macro conversion: panels, emoticons, links, status badges
- PDF generation via Playwright Chromium

## 4. Image Download Path Fix — *Claude* `Bug Fix`

Discovered that Confluence Cloud image download URLs require a `/wiki` prefix. Fixed the `download_image()` function to correctly load all attached images.

## 5. Cover Page + TOC Page Request — *User*

Requested a cover page (PinkLAB logo + title) and a table of contents page. Asked to re-apply to the test page.

## 6. Cover & TOC Page Implementation — *Claude* `Done`

- **Cover page**: PinkLAB logo + title center-aligned with page-break
- **TOC page**: Hierarchical numbering (1.1, 1.1.1...) from h1–h4 headings with anchor links
- Re-ran on test page → success

## 7. Layout Improvements + Back Page + Documentation Request — *User*

- Move PinkLAB logo to the bottom 3/4 position on the cover page
- Add a back page promoting the YouTube channel (`@pinklab_studio`)
- Create `history.html`: project history
- Create `architecture.html`: code structure documentation
- Create `README.md`: usage guide and dependencies

## 8. Final Implementation — *Claude* `Done`

- Cover page — title + meta at upper 3/4, logo at bottom 1/4
- Back page — PinkLAB logo, YouTube subscribe button, channel URL promotion
- All 3 documentation files created

## 9. Presentation Mode Request — *User*

Requested the ability to convert Confluence pages into slide-style presentation materials. H1/H2 as title slides, H3+ as content slides.

## 10. Presentation Mode Implementation — *Claude* `Done`

- **Slide HTML**: 16:9 layout with topbar (section name + title + logo) + footer (author + page number)
- **Keyboard navigation**: Space / Arrow keys / PageUp / PageDown / Home / End + mouse wheel
- **Slide PDF**: 1280×720px page size
- Title–body vertical centering with optimized spacing
- Auto-download video attachments & `<video>` tag playback support
- Print-ready `page-break` + `break-after: page` applied

## 11. Usage Documentation + Update Request — *User*

Changed token management from `.env` to `confluence_token.txt`. Organized outputs into 3 files: Document PDF + Presentation HTML + Presentation PDF. Requested updates to README, history, and architecture docs.

## 12. Final Cleanup — *Claude* `Done`

- Removed `python-dotenv` dependency; direct parsing of `confluence_token.txt`
- Streamlined to 3 output files (removed Document HTML)
- Rewrote `README.md` (installation, setup, usage, presentation controls)
- Updated `history.html` and `architecture.html`

---

*Built with Claude Code (Claude Opus 4.6) · 2026-03-04*
