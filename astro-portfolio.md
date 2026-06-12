# Skill: Astro Project Structure
## Leslie Charles — lesliecharlesmusic.com

This is a professional musician website (not just a portfolio) built with Astro on macOS Tahoe. It serves six distinct purposes: introducing Leslie, documenting his discography, advertising workshops, distributing free plugins and audio software, and providing a contact point for professional enquiries.

---

## Environment

- **OS:** macOS Tahoe 26.3.2
- **Framework:** Astro (static output)
- **Node:** managed via nvm, LTS version
- **Editor:** VS Code with the Astro extension
- **Hosting:** Netlify or Vercel
- **Domain:** lesliecharlesmusic.com

---

## File Structure

```
/
├── public/
│   ├── fonts/                    ← self-hosted WOFF2 only
│   ├── images/
│   │   ├── discography/          ← album art, WebP, square, min 600×600px
│   │   └── site/                 ← hero, about, og-cover etc.
│   └── downloads/
│       ├── plugins/              ← plugin zips/installers
│       └── software/             ← other audio tools
├── src/
│   ├── pages/
│   │   ├── index.astro           ← Home
│   │   ├── about.astro           ← About / Bio
│   │   ├── discography.astro     ← Selected Discography (album art grid)
│   │   ├── workshops.astro       ← Workshop Details
│   │   ├── downloads.astro       ← Plugins & Audio Software
│   │   └── contact.astro         ← Contact
│   ├── components/
│   │   ├── Nav.astro             ← site navigation
│   │   ├── Footer.astro          ← footer with social links
│   │   └── DiscGrid.astro        ← discography grid component
│   ├── layouts/
│   │   └── Base.astro            ← shared <head>, nav, footer, meta
│   ├── content/
│   │   ├── discography.json      ← all release entries — edit here to add releases
│   │   └── downloads.json        ← plugin/software listings — edit here to add items
│   └── styles/
│       ├── global.css            ← reset, base typography, body defaults
│       └── tokens.css            ← CSS custom properties: colour, spacing, type scale
└── astro.config.mjs
```

---

## Routes

| URL | File | Purpose |
|---|---|---|
| `/` | `index.astro` | Home — hero, short intro, nav to key sections |
| `/about` | `about.astro` | Full bio, credits, awards |
| `/discography` | `discography.astro` | Album art grid, each links externally |
| `/workshops` | `workshops.astro` | Workshop and teaching details, enquiry CTA |
| `/downloads` | `downloads.astro` | Free plugins and audio software |
| `/contact` | `contact.astro` | Email link, social links |

All routes lowercase and hyphenated. No trailing slashes (Astro default).

---

## Base Layout (Base.astro)

Every page uses `Base.astro` as its layout. It must include:

```astro
---
const { title, description, canonicalPath } = Astro.props;
const canonical = `https://lesliecharlesmusic.com${canonicalPath}`;
---
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{title}</title>
    <meta name="description" content={description}>
    <link rel="canonical" href={canonical}>

    <!-- Open Graph -->
    <meta property="og:title" content={title}>
    <meta property="og:description" content={description}>
    <meta property="og:url" content={canonical}>
    <meta property="og:image" content="https://lesliecharlesmusic.com/images/site/og-cover.jpg">
    <meta property="og:type" content="website">

    <!-- Fonts -->
    <link rel="preload" href="/fonts/your-display-font.woff2" as="font" type="font/woff2" crossorigin>

    <link rel="stylesheet" href="/styles/tokens.css">
    <link rel="stylesheet" href="/styles/global.css">
  </head>
  <body>
    <Nav />
    <main>
      <slot />
    </main>
    <Footer />
  </body>
</html>
```

Each page passes its own `title`, `description`, and `canonicalPath` as props to Base.

---

## Styling conventions

- All design values (colours, spacing, type sizes) live in `tokens.css` as CSS custom properties
- `global.css` imports `tokens.css` and sets base element styles only — no component styles
- Component-specific styles are scoped inside each `.astro` file using Astro's `<style>` tag (scoped by default)
- No Tailwind, no CSS-in-JS, no utility class frameworks
- Mobile-first: base styles target small screens, `min-width` media queries scale up
- Standard breakpoints: `768px` (tablet), `1200px` (desktop)

---

## JavaScript rules

- No JS frameworks — vanilla only
- JS is only added where interaction requires it (e.g. mobile nav toggle if needed)
- All scripts use `type="module"` or `defer`
- Analytics script (Fathom or Plausible) loads last with `defer`

---

## Adding content

**New discography entry:** edit `src/content/discography.json`
**New download item:** edit `src/content/downloads.json`
**New page:** create `src/pages/pagename.astro`, add to Nav.astro
**New image:** add WebP to appropriate subfolder under `public/images/`

Never edit content by hardcoding it into `.astro` files if it belongs in a JSON content file.
