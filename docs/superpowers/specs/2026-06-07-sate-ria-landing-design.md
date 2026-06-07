# Saté Ria — Landing Site Design

**Date:** 2026-06-07
**Status:** Approved design, pending spec review
**Source material:** `SateRia_Revival_BusinessPlan.pdf` (Revival Business Strategy 2025–2028)

## Overview

A multi-page marketing site for **Saté Ria**, Malaysia's iconic satay brand in its
2025–2028 revival. Built as a **vanilla HTML + CSS + JS** static site, deployable to
**GitHub Pages** with no build step and no backend. Pages are loaded dynamically by a
client-side **hash router**. The site is **franchise-first** (primary goal: recruit
franchisees/investors) with a secondary consumer/order surface, and supports an
**English / Bahasa Malaysia** language toggle.

### Goals
- Convert franchisees & investors ("Become a Franchisee" / enquiry CTAs).
- Communicate brand heritage, national recognition, and global ambition.
- Be fast, accessible, and self-contained on GitHub Pages.

### Non-Goals (YAGNI)
- No backend, database, or server-side rendering.
- No Menu/ordering system yet (no menu data exists; link out to Grab/ShopeeFood instead).
- No real form submission — Contact uses `mailto:` only.
- Only EN + BM filled now; the other 5 brand languages are scaffolded, not authored.

## Visual Design System

Derived from the ui-ux-pro-max design database (restaurant/heritage + premium black/gold).

- **Style:** Premium charcoal & gold heritage. Mostly solid surfaces for performance;
  subtle glass/backdrop-blur only on the sticky header and overlay elements.
- **Mode:** Dark-first hero/sections with warm lighting; light content sections for
  readability. Single cohesive theme (no separate dark-mode toggle in v1).
- **Color tokens** (CSS custom properties in `assets/css/tokens.css`):
  | Role | Hex |
  |------|-----|
  | `--color-primary` (ink/charcoal) | `#1C1917` |
  | `--color-on-primary` | `#FFFFFF` |
  | `--color-secondary` | `#44403C` |
  | `--color-accent` (gold CTA) | `#A16207` |
  | `--color-accent-hover` | `#854D0E` |
  | `--color-flame` (highlight) | `#DC2626` |
  | `--color-background` | `#FAFAF9` |
  | `--color-foreground` | `#0C0A09` |
  | `--color-muted` | `#E8ECF0` |
  | `--color-border` | `#D6D3D1` |
  - Accent gold meets WCAG ≥3:1 on dark; verify 4.5:1 for body text pairings.
- **Typography** (Google Fonts, `display=swap`, preconnect):
  - Headings: **Playfair Display SC** (400/700)
  - Body/UI: **Karla** (300–700)
  - Type scale: 12 / 14 / 16(base) / 18 / 24 / 32 / 48 / 64; line-height 1.5–1.7 body.
- **Spacing:** 4/8px rhythm. Section vertical rhythm tiers 16/24/32/48/80.
- **Container:** max-width ~1200px, adaptive gutters (16px mobile → 32px+ desktop).
- **Effects:** hover transitions 150–300ms; entrance/route transitions ≤400ms,
  `ease-out` in / `ease-in` out; all motion gated by `prefers-reduced-motion`.
- **Icons:** inline SVG sprite (Lucide-style strokes, 1.5px). **No emoji as icons.**
- **Breakpoints:** 375 / 768 / 1024 / 1440. Mobile-first.

## Architecture

### File layout
```
/
├── index.html              # App shell: <header> nav + lang toggle, <main id="app">, <footer>
├── 404.html                # Copy of index.html (GitHub Pages refresh safety net)
├── README.md               # Setup, deploy, image-prompt instructions
├── assets/
│   ├── css/
│   │   ├── tokens.css      # Color/type/spacing custom properties
│   │   └── main.css        # Layout, components, responsive, motion
│   ├── js/
│   │   ├── router.js       # Hash router: route table, fetch+inject, states
│   │   ├── i18n.js         # Language dictionary loader + DOM application
│   │   └── app.js          # Bootstrap, nav state, header, scroll, mobile menu
│   ├── img/                # User-supplied generated images (see image prompts)
│   └── icons/
│       └── sprite.svg      # SVG icon symbols
├── pages/
│   ├── home.html
│   ├── franchise.html
│   ├── about.html
│   └── contact.html
└── i18n/
    ├── en.json
    └── ms.json
```

### Router (`router.js`)
- **Hash-based** (`#/`, `#/franchise`, `#/about`, `#/contact`). Default `#/` → home.
- Route table maps hash → `pages/<name>.html`.
- On route change: show loading skeleton in `#app` → `fetch()` fragment → inject HTML
  → set active nav link (`aria-current="page"`) → scroll to top → fire a
  `route:rendered` event so i18n re-applies and section animations run.
- **Error state:** fetch failure renders an inline "couldn't load this page — retry"
  block with a retry button (no broken/blank screen).
- Intercepts in-app `<a href="#/...">` clicks via the native hashchange event.
- Unknown route → friendly inline 404 fragment (not a redirect).

### i18n (`i18n.js`)
- Strings keyed by `data-i18n="section.key"`; attributes via `data-i18n-attr`
  (e.g. `aria-label`, `placeholder`, `alt`).
- Loads `i18n/en.json` + `i18n/ms.json`; applies after every route render.
- Persists choice in `localStorage('lang')`; sets `<html lang>`; updates toggle state.
- Default **EN**. Toggle is an EN | BM segmented control in the header.

### Pages (fetched fragments — no `<html>/<head>/<body>`, just section markup)

**home.html**
1. Hero — headline "Malaysia's Iconic Satay. Reborn.", subhead, dual CTA
   (*Become a Franchisee* → `#/franchise` primary; *Discover the Brand* → `#/about` secondary).
2. Heritage strip — owned brand & secret recipe, F1 Sepang event presence,
   inspired by Tun Dr Mahathir, mobile + fixed formats.
3. Three Pillars cards — Central Kitchen / Versatile Franchise / Global Branding.
4. Franchise-formats teaser — 3 mini cards → `#/franchise`.
5. Stats band — 50+ outlets, 250K/month, RM12M+ revenue (2027), 8 markets (2028).
6. CTA band — "Join the Saté Ria Revival" → `#/contact`.

**franchise.html**
1. Central Kitchen model — 5-step flow: Raw Material → Process & Skewer → Freeze & QC
   → Distribute → Franchise Entrepreneur, plus "why it works" bullets.
2. Three format cards w/ investment + ROI:
   - Food Truck — RM 80K–120K — ROI 12–18 mo
   - Kiosk (+drive-thru) — RM 40K–70K — ROI 10–14 mo
   - Full Outlet — RM 150K–250K — ROI 18–24 mo
3. Go-to-market phases — 2025 Domestic Revival / 2026–27 National Scale / 2028 International.
4. CTA → Contact.

**about.html**
1. Brand story / heritage & market position.
2. Proprietary ownership & secret recipe; 7-language brand variants
   (Arabic, Mandarin, Hindi, Russian, Japanese, German, Korean).
3. Leadership — BO Ungku Mohammed Fadli Bin Ungku Jalil; Exclusive Promoter
   Dato' Seri Farid Ibrahim; NNF Global; inspired by Tun Dr Mahathir Mohammed.
4. Vision / financial projections band.

**contact.html**
- Contact details: info@sateria.com.my, sateria.com.my, +60 3-XXXX-XXXX.
- Primary action: **mailto:** buttons (General enquiry / Franchise enquiry with
  pre-filled subject). No form fields, no backend.
- Order channels: Grab, ShopeeFood (outbound links / placeholders).

### Images — generation-prompt placeholders
No photo assets exist. Each image slot renders a **styled placeholder** (gradient +
texture + label) that already looks intentional, and carries a **Gemini "nano banana"
generation prompt** so the user can generate and drop the real file in.

- Each `<img>` points at its final path (e.g. `assets/img/hero.jpg`) with descriptive
  `alt`, `width`/`height` (reserve space → no CLS), and `loading="lazy"` below the fold.
- The matching generation prompt lives in an **HTML comment** immediately above the
  element AND is collected in `README.md` under "Image prompts" (filename → prompt).
- A small CSS fallback (`onerror` → placeholder class) keeps the site looking polished
  until images are pasted in.
- Slots: hero, three-pillars (×3), franchise formats (×3), central-kitchen process,
  about/heritage, plus logo wordmark. Each gets a tailored prompt
  (subject, lighting = warm charcoal-grill smoke, mood = premium heritage, aspect ratio).

## Accessibility & Quality Bar
- Contrast ≥4.5:1 body / ≥3:1 large & UI glyphs; color never the sole signal.
- Visible focus rings; tab order matches visual order; skip-to-content link.
- Touch targets ≥44px; `cursor: pointer` on clickable; `touch-action: manipulation`.
- Semantic landmarks (`header/nav/main/footer`), sequential headings, labeled icon buttons.
- `prefers-reduced-motion` disables non-essential animation.
- Viewport meta set; zoom not disabled; no horizontal scroll at 375px.
- Verified at 375 / 768 / 1024 / 1440 and reduced-motion.

## Deployment
- Static; works from repo root on GitHub Pages (`main` branch / root, or `/docs`).
- Hash routing → no Pages config or rewrite rules needed; `404.html` is a safety net.
- All paths **relative** so it works under a project subpath (`user.github.io/SateRia/`).
- A `.nojekyll` file added to bypass Jekyll processing of assets.
