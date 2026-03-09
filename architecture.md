# Code Architecture — Confluence PDF Exporter

## File Structure

```
confluence_print/
  confluence_export.py       — Main script (CLI entry point)
  confluence_token.txt       — Authentication (URL, EMAIL, API_TOKEN) — not in git
  .gitignore                 — Excludes token file, output/, __pycache__/
  pinklab_logo_text.png      — PinkLAB logo for cover/back/topbar
  history.html / history.md  — Project history document
  architecture.html / architecture.md — This file
  README.md                  — Usage guide
  output/                    — Generated PDF, HTML, video files
```

## Processing Pipeline

```
CLI Input (Page URL)
  → Token File (confluence_token.txt)
  → REST API (Fetch HTML)
  → BeautifulSoup (Process Macros)
  → Fork
      ├─ Document: build_html() → Playwright → A4 PDF
      └─ Presentation: build_presentation_html() → HTML + 16:9 PDF
```

## Document PDF Structure

| # | Section    | Builder Function      | Description                                |
|---|------------|-----------------------|--------------------------------------------|
| 1 | Cover Page | `build_cover_page()`  | Title at upper 3/4, PinkLAB logo at bottom |
| 2 | TOC Page   | `build_toc_page()`    | Auto-generated from h1~h4 with numbering   |
| 3 | Content    | `build_html()`        | Processed Confluence body with styling      |
| 4 | Back Page  | `build_back_page()`   | PinkLAB YouTube channel promotion          |

## Presentation Slide Structure

| Heading | Slide Type      | Layout                                    |
|---------|-----------------|-------------------------------------------|
| Cover   | `slide-cover`   | Title + meta centered, logo at bottom     |
| H1 / H2| `slide-heading` | Title-only slide (section divider)         |
| H3+     | `slide-content` | Title + body content centered vertically. Panels auto-expand to fill slide. |
| Back    | `slide-back`    | YouTube promo slide                       |

Each slide has a topbar (section name | page title | PinkLAB logo) and footer (author | page number).

**Navigation**: Space/Arrow/PageUp/Down/Home/End + mouse wheel for slide navigation. Press **T** to open the TOC navigation overlay — shows all H1/H2/H3 headings with hierarchy for instant jumping. Arrow keys to select, Enter to jump, T/Escape to close.

## Function Reference

### Config & API Helpers
- `load_token_file()` — Read confluence_token.txt and set credentials
- `get_auth()` — Return (email, token) tuple for Basic Auth
- `extract_page_id(url)` — Parse Confluence URL to extract page ID
- `fetch_page(page_id)` — Fetch page via REST API v2
- `fetch_page_v1(page_id)` — Fallback fetch via REST API v1
- `download_image(url)` — Download image → base64 data URI

### HTML Processors (Confluence Macro → Standard HTML)
- `process_code_blocks(soup)` — Code macros → styled blocks with line numbers
- `process_panels(soup)` — Info/Note/Warning/Tip/Error panels
- `process_expand(soup)` — Expand/Collapse sections
- `process_status(soup)` — Status lozenge macros
- `process_images(soup)` — ac:image → img or video tags
- `resolve_attachment_images(soup, id)` — Download image attachments → base64 inline
- `resolve_attachment_videos(soup, id, dir)` — Download video attachments → save as files
- `process_toc(soup)` — Remove Confluence TOC macros
- `process_emoticons(soup)` — Convert to Unicode emoji
- `process_links(soup)` — ac:link → standard `<a>` tags
- `process_remaining_macros(soup)` — Graceful fallback for unknown macros

### Document Builders
- `load_logo_base64()` — Load PinkLAB logo as base64
- `build_cover_page(title, logo)` — Cover page with title + logo
- `build_toc_page(soup)` — Auto-generated TOC from headings
- `build_back_page(logo)` — YouTube promo back page
- `build_html(title, soup)` — Assemble full document HTML

### Presentation Builders
- `split_into_slides(soup)` — Split processed HTML by headings into slide data
- `build_presentation_html(data, id, soup)` — Assemble slide HTML with JS navigation + TOC overlay

### Output
- `generate_pdf(html, pdf, presentation)` — Playwright Chromium → A4 or 16:9 PDF
- `main()` — CLI entry point, orchestrates full pipeline

## Output Files (3)

| File                    | Format   | Description                                         |
|-------------------------|----------|-----------------------------------------------------|
| `{title}.pdf`           | A4 PDF   | Document: cover + TOC + content + back page          |
| `present_{title}.html`  | HTML     | Presentation: interactive slides with TOC navigation |
| `present_{title}.pdf`   | 16:9 PDF | Presentation: 1280×720px slide PDF                   |

## Dependencies

| Package          | Role                                        |
|------------------|---------------------------------------------|
| `requests`       | Confluence REST API HTTP calls              |
| `beautifulsoup4` | HTML parsing & Confluence macro transformation |
| `playwright`     | Headless Chromium for PDF rendering         |
