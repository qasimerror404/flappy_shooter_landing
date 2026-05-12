# Flappy Shooter — Landing Page

Static marketing site. Pure HTML/CSS, no build step. Deployed on Vercel.

## Quick deploy

```
npm install -g vercel    # one time
vercel login             # one time
vercel --prod
```

First run links the directory to a Vercel project (accept the prompts). Subsequent
`vercel --prod` calls just deploy. Preview deploys (no `--prod` flag) get a unique
preview URL per push.

## Local preview

```
python3 -m http.server 4000
# → http://localhost:4000
```

Or with the Vercel CLI for an environment that matches production (cleanUrls,
headers, 404):

```
vercel dev
```

## Structure

```
flappy-shooter-landing/
├── vercel.json           # hosting + security headers + cache + cleanUrls
├── README.md
├── index.html            # landing page
├── privacy.html          # privacy policy (App Store / Play Store required)
├── terms.html            # terms of service
├── 404.html              # branded not-found page (Vercel auto-serves)
├── robots.txt            # allows everything, points crawlers at sitemap
├── sitemap.xml           # / + /privacy + /terms
├── site.webmanifest      # PWA basics
└── assets/
    ├── icon.png          # PNG fallback for older browsers
    ├── icon.webp         # 12 KB — used by modern browsers
    ├── hero.png          # PNG fallback for older browsers
    └── hero.webp         # 41 KB — used by modern browsers
```

## Production hardening (already applied)

- `cleanUrls: true` → `/privacy` works without the `.html`
- Security headers on every response: `X-Content-Type-Options`, `X-Frame-Options`, `Referrer-Policy`, `Permissions-Policy` (camera/mic/geo/FLoC denied), `Strict-Transport-Security` (2-year HSTS, preload-ready)
- Cache: assets `max-age=31536000 immutable`, HTML/XML/JSON `max-age=300 must-revalidate`
- Hero served as WebP (41 KB) with PNG fallback via `<picture>` — 97% smaller than the original
- Preconnect to Google Fonts + preload hero image with `fetchpriority="high"`
- JSON-LD `MobileApplication` schema for rich search results
- OG + Twitter Card meta for social previews
- Skip-link, focus-visible outlines, semantic landmarks (`main`, `nav`, `article`)
- `prefers-reduced-motion` honored for hover transitions
- 404 page with the brand styling
- Store buttons clearly marked "Coming Soon" — bottom CTA is a mailto launch-notify

## Go-live checklist

### Current state
- **Android**: direct APK download from R2 (https://pub-cd14a0e6ff394e538cbb949e81299967.r2.dev/flappy-shotter.apk) — the green badge + bottom CTA both link to it
- **iOS**: still "Coming Soon" — App Store badge is disabled, links to mailto for launch interest

### When the APK URL changes (new version / new bucket)
- Two locations to update in `index.html`: the `<a class="store" href="…">` Android badge and the `<a class="cta-primary" href="…">` bottom CTA
- Also update the `downloadUrl` and `installUrl` in the JSON-LD `MobileApplication` schema at the top of `index.html`

### When iOS App Store ships
- Replace `<span class="store" aria-disabled="true"` (the App Store one) with `<a class="store" href="https://apps.apple.com/..."`
- Update the **Android beta available · iOS soon** pill to remove "iOS soon" once both are live
- Update the install-hint paragraph below the bottom CTA
- Update the JSON-LD `operatingSystem` from `"Android"` to `"iOS, Android"`

### When the canonical / OG URLs change
- Search-and-replace `flappy-shooter-game.web.app` → your final Vercel or custom domain across `index.html`, `sitemap.xml`, `robots.txt`

### After every deploy
- [ ] Run the URL through https://search.google.com/test/rich-results — JSON-LD should validate as `MobileApplication`
- [ ] https://www.opengraph.xyz/ — OG/Twitter card preview
- [ ] https://pagespeed.web.dev/ — should score 95+ on every axis (single static HTML with inline CSS, WebP hero, no JS)
- [ ] Submit the sitemap to Google Search Console: https://search.google.com/search-console

### Custom domain (optional)
1. Vercel dashboard → project → **Settings → Domains** → Add
2. At your DNS registrar add the CNAME/A records Vercel shows
3. HTTPS cert provisions automatically (a few minutes)
4. Search-and-replace the new domain across `index.html`, `sitemap.xml`, `robots.txt`, then redeploy

### Optional next steps
- **Analytics**: Vercel Analytics is free and cookie-less for the first 2,500 events/month. Enable in the dashboard. Or add Plausible / Firebase Analytics by hand.
- **Screenshot gallery**: drop additional `assets/screen-*.{png,webp}` and add a `<section class="gallery">` to `index.html`.
- **Open Graph image**: 1200×630 dedicated social card (currently reusing the splash artwork). Generate one with Figma or Canva.
