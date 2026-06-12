# Skill: Discography Grid
## Leslie Charles — lesliecharlesmusic.com

The discography page displays album art in a responsive grid. Each tile links externally to a streaming or purchase page. This is a functional section of the website — it documents Leslie's professional output across bass, production, engineering, and composition work.

---

## Data source

All entries live in `src/content/discography.json`. This is the only file to edit when adding or updating releases.

```json
[
  {
    "artist": "Thermal And A Quarter",
    "album": "Status: Migrane",
    "role": ["Bass"],
    "art": "/images/discography/taaq-status-migrane.webp",
    "link": "https://taaq.bandcamp.com/album/status-migrane"
  },
  {
    "artist": "Leslie Charles",
    "album": "Beat Tape Vol. 1",
    "role": ["Production", "Composition"],
    "art": "/images/discography/lc-beat-tape-vol1.webp",
    "link": "https://..."
  }
]
```

**Valid role values:** `"Bass"`, `"Guitar"`, `"Production"`, `"Engineering"`, `"Mixing"`, `"Mastering"`, `"Composition"`, `"Sound Design"`

**Source:** This data comes from Leslie's Excel spreadsheet. When populating `discography.json`, copy artist, album, and role directly from the sheet — do not infer or fill in missing values. If a field is blank in the sheet, ask before guessing.

---

## Image requirements

- Format: WebP only
- Dimensions: square, minimum 600×600px
- Location: `/public/images/discography/`
- Naming: `artist-album-name.webp` — all lowercase, hyphens, no spaces
- Never use JPG or PNG in the grid — convert before adding

---

## Component: DiscGrid.astro

Location: `src/components/DiscGrid.astro`

```astro
---
import discography from '../content/discography.json';
---

<ul class="disc-grid">
  {discography.map(item => (
    <li class="disc-item">
      <a href={item.link} target="_blank" rel="noopener noreferrer">
        <figure>
          <img
            src={item.art}
            alt={`${item.album} by ${item.artist}`}
            width="600"
            height="600"
            loading="lazy"
          />
          <figcaption>
            <span class="disc-artist">{item.artist}</span>
            <span class="disc-album">{item.album}</span>
            <span class="disc-role">{item.role.join(', ')}</span>
          </figcaption>
        </figure>
      </a>
    </li>
  ))}
</ul>
```

- `rel="noopener noreferrer"` is required on every `target="_blank"` link — do not omit
- `alt` text format: `"{album} by {artist}"` — always both, always in this order
- `loading="lazy"` on all images — the grid can be long
- Use `<ul>` / `<li>` for the grid — it's a list of items, semantically correct

---

## CSS

```css
/* Grid container */
.disc-grid {
  list-style: none;
  padding: 0;
  margin: 0;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
  gap: 1rem;
}

/* Each tile */
.disc-item a {
  display: block;
  text-decoration: none;
}

.disc-item figure {
  position: relative;
  overflow: hidden;
  margin: 0;
}

.disc-item img {
  width: 100%;
  aspect-ratio: 1;
  object-fit: cover;
  display: block;
}

/* Caption: always visible on mobile */
.disc-item figcaption {
  padding: 0.5rem 0;
  display: flex;
  flex-direction: column;
  gap: 0.15rem;
}

.disc-artist {
  font-size: 0.8rem;
  font-weight: 600;
}

.disc-album {
  font-size: 0.75rem;
  opacity: 0.7;
}

.disc-role {
  font-size: 0.7rem;
  opacity: 0.5;
}

/* Desktop: switch caption to hover overlay */
@media (min-width: 768px) {
  .disc-item figcaption {
    position: absolute;
    inset: 0;
    padding: 1rem;
    opacity: 0;
    transition: opacity 0.2s ease;
    justify-content: flex-end;
    /* background: set this to match your design tokens */
  }

  .disc-item:hover figcaption {
    opacity: 1;
  }
}
```

**Why two caption modes:** On mobile there is no hover state — tapping once triggers hover, tapping again follows the link, which is confusing UX. Static caption below the image on mobile avoids this. The overlay switches on at the 768px breakpoint where a pointer device is expected.

---

## Filtering by role (optional)

If role filtering is added later, implement it as a CSS class toggle — no JS framework, no page reload:

```js
// vanilla JS — add to a <script> tag in discography.astro
document.querySelectorAll('.filter-btn').forEach(btn => {
  btn.addEventListener('click', () => {
    const role = btn.dataset.role;
    document.querySelectorAll('.disc-item').forEach(item => {
      const roles = item.dataset.roles.split(',');
      item.style.display = (role === 'all' || roles.includes(role)) ? '' : 'none';
    });
  });
});
```

Each `<li>` would need `data-roles={item.role.join(',')}` added to it in the component.

---

## What not to do

- Do not use a carousel or slider — the grid is the right pattern for a large collection
- Do not lazy-load the first 6–8 images (above the fold) — set `loading="eager"` on the first row, `loading="lazy"` on the rest
- Do not link to the image file itself — every tile must link to the external release page
- Do not hardcode entries in the `.astro` file — all entries go in `discography.json`
