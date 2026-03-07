# Project History — Confluence PDF Exporter

## 1. User Request — Confluence PDF Quality Improvement
- Confluence Cloud's built-in PDF exporter produces poor output; code blocks lack line numbers.
- Requested a tool using MCP to connect to Confluence and convert pages to HTML/PDF.
- Domain: `https://pinkwink.atlassian.net/`, Output: HTML + PDF

## 2. Claude — Approach Design & Planning
- Chose **REST API + Python script** over Confluence MCP Server or WebFetch.
- Created conda environment `confluence` (Python 3.11).
- Packages: `requests`, `beautifulsoup4`, `playwright`, `python-dotenv`.

## 3. Claude — Core Script Implementation
- Wrote `confluence_export.py`: REST API v2/v1 auto-switch, page ID extraction from URL, code block line numbers, base64 image embedding, macro conversion, PDF generation via Playwright Chromium.

## 4. Claude — Image Download Path Fix
- Fixed image download URL requiring `/wiki` prefix for Confluence Cloud.

## 5. User Request — Cover Page + TOC Page
- Requested a title page (PinkLAB logo + title) and a table of contents page.

## 6. Claude — Cover & TOC Implementation
- Cover page: PinkLAB logo + centered title with page-break.
- TOC page: hierarchical numbering (1.1, 1.1.1...) with anchor links.

## 7. User Request — Layout Improvement + Last Page + Documentation
- Move PinkLAB logo to bottom 1/4 of cover page.
- Add YouTube channel promotion (`@pinklab_studio`) on the last page.
- Create `history.html`, `architecture.html`, `README.md`.

## 8. Claude — Final Implementation
- Cover: title+meta at top 3/4, logo at bottom 1/4.
- Last page: PinkLAB logo, YouTube subscribe button, channel URL.
- All 3 documents created.

## 9. User Request — Presentation Mode
- Convert Confluence pages into slide-style presentations. H1/H2 as title slides, H3+ as content slides.

## 10. Claude — Presentation Mode Implementation
- 16:9 slide layout with topbar (section + title + logo) and footer (author + page number).
- Keyboard navigation: Space/Arrow/PageUp/Down/Home/End + mouse wheel.
- Slide PDF: 1280×720px page size.
- Video attachment auto-download & `<video>` tag playback support.

## 11. User Request — Usage Cleanup + Documentation Update
- Switch token management from `.env` to `confluence_token.txt`.
- Consolidate output to 3 files: Document PDF + Presentation HTML + Presentation PDF.

## 12. Claude — Final Cleanup
- Removed `python-dotenv` dependency, direct `confluence_token.txt` parsing.
- 3 output files (removed Document HTML).
- Rewrote `README.md`, updated `history.html` and `architecture.html`.

## 13. User Request — Presentation Image Scaling + Cover Cleanup
- Small images in presentations displayed at original small size — requested scaling up to fit slides.
- Remove "Exported from Confluence", URL, and date meta info from the cover slide.

## 14. Claude — Presentation Image Scaling & Cover Cleanup
- **Image scaling**: Removed inline `width`/`height` attributes from images in `build_presentation_html()`. Added CSS `min-height: 200px` so small images scale up to fill available slide space.
- **Cover cleanup**: Removed `cover-meta` section (Exported from Confluence, URL, date) from the cover slide — now shows only title + divider + logo.

## 15. User Request — Document PDF Cover Meta Removal
- Remove "Exported from Confluence", URL, and date info from the document PDF cover page as well.

## 16. Claude — Document PDF Cover Meta Removal
- Removed `cover-meta` div from `build_cover_page()`. Cleaned up unused `from datetime import date` import and `today` variable.

## 17. User Request — UNKNOWN_ATTACHMENT Image Fix
- Reported broken images on a specific page where Confluence stored filenames as `UNKNOWN_ATTACHMENT`.

## 18. Claude — UNKNOWN_ATTACHMENT Fallback
- Root cause: Confluence paste bug stores `ri:filename="UNKNOWN_ATTACHMENT"` while `ac:alt` contains the real filename.
- Added fallback in `process_images()`: when `ri:filename` is `UNKNOWN_ATTACHMENT`, extract the real filename from `ac:alt`.

## 19. User Request — Presentation TOC Navigation (2026-03-07)
- Requested a keyboard-triggered TOC popup for quick slide navigation in presentations.
- TOC should list H1, H2, H3 headings with hierarchy and allow jumping to any slide.

## 20. Claude — TOC Navigation Implementation
- **TOC overlay**: Floating panel triggered by `T` key, showing all heading slides (H1/H2/H3) with hierarchical indentation.
- **Keyboard navigation**: Arrow keys to move selection, Enter to jump, T/Escape to close.
- **Visual design**: Semi-transparent backdrop, animated panel, current slide highlighted, page numbers shown.
- **CSS**: Added `.toc-overlay`, `.toc-panel`, `.toc-item` styles with level-based indentation. Hidden in print.
- **Data attributes**: Each slide with a heading gets `data-toc-level` and `data-toc-title` for JS-based TOC building.
