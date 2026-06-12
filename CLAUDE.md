# CLAUDE.md — Leslie Charles Website
## lesliecharlesmusic.com · Technical Best Practices

---

## Skills files

Detailed specifications for specific parts of this project live in separate skill files. Always read the relevant skill before working on that part of the site:

| Skill | File | Use when |
|---|---|---|
| Project structure & conventions | `skills/astro-portfolio.md` | Starting any session, adding pages or components |
| Discography grid | `skills/discography-grid.md` | Working on `/discography` |
| Downloads page | `skills/downloads-page.md` | Working on `/downloads` |

**`astro-portfolio.md` should be read at the start of every session** — it defines the file structure, routing, content JSON shapes, and styling conventions everything else depends on.

---

## Stack

- **Framework:** Astro (static output, zero JS by default)
- **Styling:** Vanilla CSS with custom properties — no Tailwind, no CSS-in-JS
- **JS:** Vanilla only. Use it only where interaction is unavoidable
- **Hosting:** Vercel or Netlify — free tier, CI/CD from Git, custom domain support
- **Domain:** `lesliecharlesmusic.com` (already purchased)

**Local environment: macOS Tahoe (26.3.2)**
- Install Node.js via [nvm](https://github.com/nvm-sh/nvm) — do not use the installer from nodejs.org:
  ```bash
  curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
  nvm install --lts
  nvm use --lts
  ```
- Install [Homebrew](https://brew.sh) if not already present
- Use VS Code with the **Astro** extension (`astro-build.astro-vscode`)
- If you hit npm permission errors, do not use `sudo npm` — the fix is ensuring nvm owns your Node install, not the system

---

## Performance

**Images**
- WebP format for everything
- Always set `width` and `height` attributes to prevent layout shift
- `loading="lazy"` below the fold, `loading="eager"` on above-the-fold images
- Use `srcset` for responsive sizes — Astro's `<Image />` component handles this

**Fonts**
- Self-host as WOFF2 — never load from Google Fonts or Adobe Fonts CDN (render-blocking)
- Subset to used glyphs: [Font Squirrel subsetter](https://www.fontsquirrel.com/tools/webfont-generator)
- `font-display: swap` on all `@font-face` declarations
- Preload the display font in `<head>`:
  ```html
  <link rel="preload" href="/fonts/your-display-font.woff2" as="font" type="font/woff2" crossorigin>
  ```

**General**
- Lighthouse score ≥ 90 on mobile before launch — check before every deploy
- No render-blocking scripts — use `defer` or `type="module"`
- No third-party scripts except analytics, loaded last with `defer`

---

## SEO

Every page needs its own title, description, and canonical in `Base.astro` — see `skills/astro-portfolio.md` for the full `<head>` implementation.

**Structured Data** — add once to `index.astro`:
```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Person",
  "name": "Leslie Charles",
  "jobTitle": "Music Producer, Recording Engineer, Bassist",
  "url": "https://lesliecharlesmusic.com",
  "sameAs": [
    "https://www.instagram.com/yourhandle",
    "https://bandcamp.com/yourpage"
  ],
  "award": [
    "IRAA Best Rock & Blues Producer 2022",
    "IRAA Best Recording Engineer 2025",
    "Best Bass Player — The Indies 2021"
  ],
  "worksFor": {
    "@type": "Organization",
    "name": "Stained Class Productions"
  }
}
</script>
```

**URLs:** all lowercase, hyphenated, no trailing slashes. Submit `sitemap.xml` to Google Search Console — add `@astrojs/sitemap` to generate it automatically.

---

## Accessibility

- Descriptive `alt` text on all images — not `alt="photo"`
- WCAG AA contrast minimum — test at [webaim.org/resources/contrastchecker](https://webaim.org/resources/contrastchecker)
- Visible focus states on all interactive elements — never `outline: none` without a replacement
- Semantic HTML throughout: `<nav>`, `<main>`, `<section>`, `<article>`, `<footer>`
- Fully keyboard navigable
- Respect reduced motion:
  ```css
  @media (prefers-reduced-motion: reduce) {
    *, *::before, *::after {
      animation-duration: 0.01ms !important;
      transition-duration: 0.01ms !important;
    }
  }
  ```

---

## Responsive Design

- Mobile-first: base styles target small screens, `min-width` breakpoints scale up
- Breakpoints: `768px` tablet, `1200px` desktop
- Fluid typography with `clamp()`:
  ```css
  font-size: clamp(1rem, 2.5vw, 1.25rem);
  ```
- Minimum touch target size: 44×44px
- Test on an actual phone, not just browser resize

---

## Contact Page

```html
<a href="mailto:hello@lesliecharlesmusic.com">hello@lesliecharlesmusic.com</a>
```

No form needed at this scale. If one is added later, use [Netlify Forms](https://docs.netlify.com/forms/setup/) — free, no backend code required.

---

## Analytics

[Fathom](https://usefathom.com) or [Plausible](https://plausible.io) — privacy-first, no cookie banner needed, ~1KB. Do not use Google Analytics.

---

## Deployment

```bash
npm run build      # outputs to /dist
npm run preview    # check locally before pushing
# push to main → Netlify/Vercel auto-deploys
```

**Domain setup (`lesliecharlesmusic.com`):**
- Add the domain in your Netlify/Vercel dashboard
- Add the DNS records they provide at your registrar
- Add `www.lesliecharlesmusic.com` and redirect it to the apex (or vice versa — pick one)
- SSL via Let's Encrypt, automatic, ~1 minute

**Never commit `.env` to Git.** Add environment variables in the Netlify/Vercel dashboard instead.

---

## What to Avoid

| Avoid | Why |
|---|---|
| WordPress | Maintenance burden, slower, overkill |
| jQuery | Unnecessary — vanilla JS covers everything |
| CSS frameworks (Bootstrap, Tailwind) | Designing it yourself — custom CSS is cleaner |
| Google Fonts | Render-blocking network request |
| `loading="lazy"` on hero images | Delays above-the-fold content |
| `<iframe>` embeds for music players | Heavy, slow, breaks on mobile |
| `sudo npm` | Masks a broken Node setup — fix nvm instead |
| Deploying without running Lighthouse | Always check before going live |

---

*Last updated: June 2026*
