# Confluence PDF Exporter

A Python CLI tool that converts Confluence Cloud pages into **document PDFs** and **presentations (slides)**.

It addresses the limitations of Confluence's built-in PDF exporter (e.g., missing line numbers in code blocks, poor styling) and allows pages to be used directly as presentation materials.

## Features

- **Document PDF** — Cover page + auto-generated TOC + body + back page
- **Presentation HTML** — Slide-based presentation (keyboard/mouse navigation)
- **Presentation PDF** — 16:9 slide PDF
- Line numbers in code blocks
- Base64 inline image embedding (standalone)
- Automatic video attachment download & playback support
- Confluence macro conversion (panels, expand, status, emoticon, links, etc.)

## Prerequisites

- [Conda](https://docs.conda.io/) (Miniconda or Anaconda)
- Confluence Cloud account + API Token

## Setup

### 1. Create Conda Environment

```bash
conda create -n confluence python=3.11 -y
conda activate confluence
```

### 2. Install Packages

```bash
pip install requests beautifulsoup4 playwright
playwright install chromium
```

### 3. Configure Authentication

Create a `confluence_token.txt` file in the project root:

```
CONFLUENCE_URL=https://your-domain.atlassian.net
CONFLUENCE_EMAIL=your-email@example.com
CONFLUENCE_API_TOKEN=your-api-token-here
```

> You can generate an API Token at [Atlassian API Token Management](https://id.atlassian.com/manage-profile/security/api-tokens).

## Usage

```bash
conda activate confluence

# Basic usage
python confluence_export.py "https://your-domain.atlassian.net/wiki/spaces/SPACE/pages/PAGE_ID/Page+Title"

# Specify output directory
python confluence_export.py "PAGE_URL" --output-dir ./my_output
```

### Output

Three files are generated in the `output/` directory:

| File | Description |
|------|-------------|
| `Page_Title.pdf` | A4 document PDF (cover + TOC + body + back page) |
| `present_Page_Title.html` | Presentation HTML (can be presented directly in a browser) |
| `present_Page_Title.pdf` | Presentation PDF (16:9 slides) |

### Presentation Controls

Open the presentation HTML in a browser to use it as a slideshow:

| Action | Key |
|--------|-----|
| Next slide | `Space`, `→`, `↓`, `PageDown` |
| Previous slide | `←`, `↑`, `PageUp` |
| Go to first slide | `Home` |
| Go to last slide | `End` |
| Mouse wheel | Scroll up/down to navigate slides |

## Dependencies

| Package | Version | Role |
|---------|---------|------|
| `requests` | >= 2.28 | Confluence REST API calls |
| `beautifulsoup4` | >= 4.12 | HTML parsing and Confluence macro conversion |
| `playwright` | >= 1.40 | Headless Chromium PDF rendering |

## File Structure

```
confluence_print/
├── confluence_export.py      # Main script (CLI entry point)
├── pinklab_logo_text.png     # PinkLAB logo (cover/back pages)
├── confluence_token.txt      # Auth config (not in git)
├── .gitignore
├── README.md
├── history.html              # Project development history
├── architecture.html         # Code architecture document
└── output/                   # Generated files
```

## Supported Confluence Macros

| Macro | Conversion |
|-------|-----------|
| `code` | Styled code block with line numbers |
| `info` / `note` / `warning` / `tip` / `error` | Colored panels |
| `expand` | Expand/Collapse sections (shown expanded) |
| `status` | Colored status lozenges |
| `toc` | Removed (custom TOC page generated) |
| `ac:image` | Base64 inline embedded images |
| `ac:image` (video) | Video file download & `<video>` tag embedding |
| `ac:link` | Standard HTML `<a>` tags |
| `ac:emoticon` | Unicode emoji |
| Others | Graceful fallback (body content preserved) |
