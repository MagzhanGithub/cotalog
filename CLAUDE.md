# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

Static single-page mobile catalog for **Khatima** — a premium retail equipment manufacturer. No build step, no dependencies, no package manager. Open `index.html` directly in a browser to preview.

Deployed on Vercel: push to `master` → auto-deploys to `https://khatimacotalog.vercel.app/`

## Architecture

Everything lives in one file: [index.html](index.html). Inline CSS, inline JS, all 13 page definitions. No framework, bundler, or external stylesheet.

**Page system** — pages are `<div class="pg">` elements stacked absolutely inside `#stage` (390×700 px reference frame). JS scales `#stage` via `transform: scale()` to fit any screen (`fit()` function). Only the active page is visible; others carry class `off` (`display: none`).

**Flip animation** — custom 3D CSS flip, no library. `flip(to, dir)` in the IIFE:
- `dir > 0` (forward): current page pivots left-edge, folds to `rotateY(-90deg)` with ease-in; incoming page pivots right-edge, unfolds from `rotateY(90deg)` to `0` with ease-out. Overlap at 270 ms, total 640 ms.
- `dir < 0` (backward): mirrored. `perspective: 1400px` on `#stage-wrap` provides 3D depth.

**Page order:**
0. Cover (`.pg-cv`) — dark gradient, no header strip
1. About (`.pg-ab`) — company info
2–11. Products — `.ph` header + `.pp` photo + `.pi` info block with `.cat`, `.nam`, `.dsc`, `.tags`
12. Contacts (`.pg-ct`) — dark gradient, no header strip

**CSS class convention:** short abbreviations — `pg` (page), `ph` (page header), `pp` (photo panel), `pi` (product info), `pp-lg`/`pi-sm` (tall-photo variant), `cv-*` (cover), `ab-*` (about), `ct-*` (contacts), `cat/nam/dsc/tag` (product fields).

## Image directories

| Dir | Purpose |
|-----|---------|
| `ci/` | Active product images used by catalog (`p01.jpg`–`p10.jpg`) |
| `images/` | Logo (`logo_khatima.PNG`) + original scanned pages (`page_01.webp`–`page_13.webp`, unused) |
| `img/` | Raw source pool — 80 WhatsApp-exported product photos; pick from here when adding pages |
| `js/` | `page-flip.min.js` — bundled but not used (replaced by custom animation) |

## Adding a product page

1. Copy the chosen photo from `img/` to `ci/pNN.jpg`
2. Insert a new `<div class="pg off">` block before the contacts page (index 12), following the pattern of pages 2–11
3. Update the `<s>` counter in `.ph` on all product pages (e.g. `11 / 11`)
4. No JS changes needed — `querySelectorAll('.pg')` picks up the new page automatically

## Deployment

```bash
vercel deploy --prod --yes   # deploy to khatimacotalog.vercel.app
```

Vercel project: `khatima_cotalog` (linked via `.vercel/project.json`). Do not switch to a different Vercel project.
