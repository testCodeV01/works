# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Static portfolio site for TKG (freelance IT engineer), published via GitHub Pages at `https://works.tk-labo.com/` (custom domain set by `CNAME`).

The site is a single `index.html` file with **no CSS framework and no build step**. All CSS is inline `<style>` and all JS is inline `<script>`. The only external assets are Google Fonts and the GA tag.

## Structure

```
index.html        # Entire site — one file
CNAME             # works.tk-labo.com
robots.txt        # Points crawlers at the sitemap
sitemap.xml       # Single URL; bump <lastmod> when the page ships
assets/
  tkgpic.jpg      # Profile photo
  icon.png        # Favicon (512x512)
  ogp.png         # OGP / Twitter card image (1200x630)
  sciencehub.png  # ScienceHub icon (Works section)
```

`assets/ogp.png` is generated, not hand-drawn — it reuses the site's palette, grid and type. Regenerate it (rather than editing the PNG) whenever the headline stats in it go stale.

## Design system

Since v3.0.0 the site follows the **ScienceHub** look (same author's product at `sciencehub.tk-labo.com`, whose guide is `docs/design-guidelines.md` in that repo), with one deliberate difference: **no rounded corners anywhere** (ScienceHub uses `.5rem` / `999px`; here everything is square). Do **not** reintroduce Bootstrap or another CSS framework — everything is hand-authored CSS Grid/Flex.

- **Tokens**: all colors are CSS custom properties on bare `:root`. Dark mode redefines only the tokens, in both `@media (prefers-color-scheme:dark) :root:not([data-theme="light"])` and `:root[data-theme="dark"]` (a nav button toggles the latter). Never declare a color solely inside a media or `[data-theme]` block. Values are taken from ScienceHub's `frontend/css/tokens.scss`.
- **Theme colors, chosen by meaning** (as in ScienceHub):
  - `--teal` — main. Nav/footer band, hero, rules, bars, markers, primary buttons (`.btn-main`). Not for body text (≈3:1 on white).
  - `--yellow` — "ask / take part". The hire-me CTA (`.btn-fill`, nav Contact) and small highlight tags. Text on yellow is always `--on-yellow`, never white.
  - `--maroon` — caution only: required-field marks and form errors.
- **Neutrals**: `--bg`/`--surface` white, `--surface-2` neutral grey for caption/footer strips, `--ink`…`--ink-4` text, `--line`/`--line-strong` 1px rules.
- **Type**: body uses ScienceHub's system stack (`--f-sans`); `--f-cond` (IBM Plex Sans Condensed) for the hero name and large numerals; `--f-mono` (IBM Plex Mono) for labels, dates and tags.
- **Section heading**: `.rail` sits above the content — outlined two-digit number, EN label with a teal square, JA `<h2>`, and a teal bar on the bottom rule.
- **Figures**: numbered `fig.N` in appearance order. `fig.1` is the skills usage-history chart, `fig.2` the process diagram (drawn as a Feynman diagram — vertices are the dev phases). The hero also carries a decorative Feynman diagram (`.hero-diagram`, `aria-hidden`).

### Motion

All motion lives under `@media (prefers-reduced-motion:no-preference)` and behind the `.js` class, so it is off for reduced-motion users and content is visible without JS.

- Hero: name splits into letters that rise in; other hero lines fade up in sequence; the background grid drifts; the Feynman diagram draws itself.
- Items marked `.st` (added by JS from a selector list) fade up when they enter the viewport, staggered within each batch. `.rail` draws its bar.
- `fig.1` bars grow from the left; `fig.2` line draws and vertices pop in order.
- Stats count up; a yellow scroll-progress bar runs under the nav; cards lift on hover.

## Deployment

Push to `main` branch → GitHub Pages auto-deploys from repo root (`/`).
No build, no CI. Changes are live within a few minutes of push.

## Contact Form

The form POSTs JSON to an external Rails API:

```
POST https://sciencehub.tk-labo.com/api/external_contacts/receive
Content-Type: application/json
```

The Rails app (separate repo at `sciencehub.tk-labo.com`) handles delivery to `qft-tk-1213@tk-labo.com`. The path above is the one `index.html` has always called and is confirmed correct — an earlier version of this file wrongly documented it as `/api/science_hub/external_contacts/receive`.

CORS on that server must allow the site's origin, `works.tk-labo.com` (not the old `testCodeV01.github.io`). That change is made in the ScienceHub repo, not here.

## Key URLs

- **Live site**: `https://works.tk-labo.com/`
- **GitHub**: `https://github.com/testCodeV01`
- **Contact API**: `https://sciencehub.tk-labo.com/api/external_contacts/receive`

## Identity and privacy

The site publishes under the handle **TKG** only. The owner's real name, age, and nearest station are deliberately withheld — do not add them, even though they appear in the source skill sheet.

Client-side details are generalised on purpose: former employers are described by category only ("メーカー系IT企業"), and client projects are identified by industry, team size, and role — never by client name or a fingerprinting combination of product name plus niche vendor. Career facts come from the owner's skill sheet; when adding an entry, carry over the capability and drop anything that identifies the client or exposes their internal affairs (personnel moves, technical debt, incidents).

## Versioning

The footer carries a version and a last-updated date:

```html
<span class="ver">v3.0.0</span> · 更新 2026.10.31
```

**Bump both on every change that ships**, in the same commit as the change itself — a stale version string is worse than none. `v2.0.0` is the hand-authored redesign that replaced the Bootstrap build (2026-08-23). `v3.0.0` (2026-10-31) moved the look to the ScienceHub style, square-cornered, with scroll motion.

- **MAJOR** — visual redesign or a change to the section structure
- **MINOR** — a section added or removed, a substantial content update (new project, reworked skills chart), or a new UI feature
- **PATCH** — wording fixes, styling tweaks, asset optimisation, bug fixes

The date is the ship date in `YYYY.MM.DD`. There is no build step, so nothing generates this — it is edited by hand in `index.html`.

## Page Sections

`#hero` → `#about` → `#skills` → `#works` → `#career` → `#services` → `#contact`

Navigation highlights the active section via `IntersectionObserver`; a second observer reveals `.st` items and `.rail` headings on scroll.
Sections are numbered `01`–`06` in the heading (About through Contact); renumber if a section is added or reordered.

## SEO

- JSON-LD `Person` schema in `<head>`
- OGP + Twitter Card meta tags
- `<link rel="canonical">` pointing to `https://works.tk-labo.com/`
- All section headings follow h1 → h2 → h3 hierarchy
- The single `<h1>` holds both the name and the primary keyword: `TKG` in `.h1-name`, `Ruby on Rails 受託開発 …` in `.h1-role`. Keep the keyword phrase inside the `<h1>` — the name alone matches nothing anyone searches for.
- `<title>` leads with the keyword and ends with the name; keep it under ~35 full-width characters so it is not truncated.
- JSON-LD `Person` carries `hasOccupation` and `makesOffer` (a `Service`), so the offering is machine-readable as well as the person.
