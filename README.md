# Saté Ria — Landing Site

Static marketing site for **Saté Ria**, Malaysia's iconic satay brand (Revival
2025–2028). Vanilla HTML/CSS/JS, a hash-based router for dynamic page loading, and an
**EN / BM** language toggle. No build step, no backend — deploys straight to GitHub Pages.

## Run locally

The router fetches HTML fragments, so you must serve over HTTP (opening `index.html`
via `file://` will be blocked by CORS). Any static server works:

```bash
# Python
python -m http.server 8000
# then open http://localhost:8000

# or Node
npx serve .
```

## Deploy to GitHub Pages

1. Push to GitHub.
2. **Settings → Pages → Build and deployment → Source: Deploy from a branch**.
3. Branch: `main`, folder: `/ (root)`. Save.
4. Site goes live at `https://<user>.github.io/<repo>/`.

All asset paths are **relative**, so it works under a project subpath. `404.html`
redirects deep links back into the hash router, and `.nojekyll` stops Jekyll from
touching the `assets/` folder.

## Project structure

```
index.html        App shell: header (nav + lang toggle), <main>, footer
404.html          Redirects unknown paths into the hash router (GH Pages safety net)
.nojekyll         Disable Jekyll processing
assets/
  css/  tokens.css   Design tokens (color, type, spacing, motion)
        main.css     Layout, components, responsive, animations
  js/   i18n.js      data-i18n dictionary translator (localStorage-backed)
        router.js    Hash router: #/route -> pages/<file>.html
        app.js       Bootstrap, header, mobile nav, reveal + counters, sprite loader
  icons/ sprite.svg  Inline SVG icon symbols (no emoji)
  images/            <-- drop generated images here (see "Images" below)
pages/  home.html franchise.html about.html contact.html   Routed fragments
i18n/   en.json ms.json   Translation dictionaries
```

## Editing content

- **Text** lives in `i18n/en.json` and `i18n/ms.json`, keyed by the `data-i18n`
  attributes in the page fragments. Edit both files to keep languages in sync.
- **Add a page:** create `pages/foo.html` (just section markup, no `<html>` wrapper),
  add a route in `assets/js/router.js` (`ROUTES`), and a nav link in `index.html`.

## Images (Gemini "nano banana" prompts)

Every image slot currently shows a styled placeholder. To make it real, generate the
image, name it exactly as below, and drop it into `assets/images/`. It will appear
automatically (the placeholder hides itself once the file loads).

| File (`assets/images/…`) | Aspect | Prompt |
|---|---|---|
| `hero.jpg` | 16:9 | Cinematic close-up of Malaysian chicken & beef satay skewers grilling over glowing charcoal, dramatic side lighting, curling smoke, embers, shallow depth of field, premium food photography, dark moody background, warm amber & ember-orange highlights, no text. |
| `heritage.jpg` | 5:4 | Nostalgic yet premium scene of a Malaysian satay vendor's grill at a bustling night market, hands fanning charcoal smoke, golden lantern light, rich browns and gold tones, documentary-heritage mood, slight film grain, no text. |
| `format-truck.jpg` | 16:10 | A sleek branded black-and-gold Saté Ria food truck at a vibrant outdoor festival at dusk, warm string lights, smoke from the grill, premium street-food vibe, no readable text/logo. |
| `format-kiosk.jpg` | 16:10 | A modern compact Saté Ria kiosk inside a bright Malaysian mall food court, charcoal-and-gold branding, glass display of skewers, clean premium QSR design, no readable text/logo. |
| `format-outlet.jpg` | 16:10 | A standalone Saté Ria flagship drive-thru restaurant at golden hour, warm interior glow, outdoor seating, charcoal-and-gold premium signage architecture, inviting, no readable text/logo. |
| `story.jpg` | 5:4 | Artful overhead flat-lay of a Malaysian satay spread: skewers, peanut sauce in a brass bowl, ketupat rice cakes, sliced cucumber and onion, banana leaf, dark rustic table, warm directional light, premium editorial food styling, no text. |
| `person-bo.jpg` | 1:1 | Dignified studio portrait of a distinguished Malaysian businessman in a dark suit, warm soft lighting on a charcoal background, premium corporate headshot, respectful and confident. |
| `person-promoter.jpg` | 1:1 | Dignified studio portrait of a distinguished Malaysian gentleman, dark formal attire, warm soft lighting on charcoal background, premium corporate headshot. |
| `logo-nnf.jpg` | 1:1 | Minimalist premium logo lockup for "NNF Global" in gold on a charcoal background, elegant serif wordmark, generous spacing. |
| `og-cover.jpg` | 1.91:1 | Saté Ria social share cover: satay skewers over charcoal with subtle gold "Saté Ria" wordmark space, dark premium look. (Used for link previews.) |

> The same prompts are duplicated as HTML comments directly above each image in the
> `pages/*.html` files, so you can find them in context too.

## Notes

- **Contact** uses `mailto:` links only (pre-filled subjects/bodies) — no backend.
  Email `admin@sateria.my`, domain `sateria.my`. Update the phone (`+60 3-XXXX-XXXX`)
  in `pages/contact.html` and the `index.html` footer when finalised.
- Grab / ShopeeFood buttons on Contact are placeholders (`href="#"`) — point them at
  real ordering URLs when available.
- **Brand logo** is `assets/images/logo.png` (header, footer, favicon).
- **Heritage gallery** on the Heritage page uses real archival photos
  `assets/images/36–40.jpg` (2008 meeting with Tun Dr Mahathir Mohamad).
- **Leadership portraits** (`person-bo.jpg`, `person-promoter.jpg`) are still
  placeholders — drop those files in to replace them, or swap in real photos.
- **Motion:** animations are intentionally forced ON and ignore the user's
  `prefers-reduced-motion` setting (per request). To restore the accessible default,
  flip `reduceMotion` back in `assets/js/app.js` and re-add the reduced-motion media
  query in `assets/css/main.css` (see the "Motion" banner there).
- Accessibility: keyboard-navigable, AA-contrast targets, skip link, semantic landmarks.
