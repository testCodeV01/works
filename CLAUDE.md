# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Static portfolio site for TKG (freelance IT engineer), published via GitHub Pages at `https://testCodeV01.github.io/`.

The site is a single `index.html` file using Bootstrap 5 (CDN) with no build step. All CSS is inline `<style>` and all JS is inline `<script>`.

## Structure

```
index.html        # Entire site — one file
assets/
  tkgpic.jpg      # Profile photo
```

## Deployment

Push to `main` branch → GitHub Pages auto-deploys from repo root (`/`).
No build, no CI. Changes are live within a few minutes of push.

## Contact Form

The form POSTs JSON to an external Rails API:

```
POST https://sciencehub.tk-labo.com/api/science_hub/external_contacts/receive
Content-Type: application/json
```

The Rails app (separate repo at `sciencehub.tk-labo.com`) handles delivery to `qft-tk-1213@tk-labo.com`. CORS must allow `testCodeV01.github.io` on that server.

## Key URLs

- **Live site**: `https://testCodeV01.github.io/`
- **GitHub**: `https://github.com/testCodeV01`
- **Contact API**: `https://sciencehub.tk-labo.com/api/science_hub/external_contacts/receive`

## Page Sections

`#hero` → `#about` → `#skills` → `#works` → `#career` → `#services` → `#contact`

Navigation highlights the active section via `IntersectionObserver`.

## SEO

- JSON-LD `Person` schema in `<head>`
- OGP + Twitter Card meta tags
- `<link rel="canonical">` pointing to the GitHub Pages URL
- All section headings follow h1 → h2 → h3 hierarchy
