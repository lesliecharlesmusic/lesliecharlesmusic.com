# Skill: Downloads Page
## Leslie Charles — lesliecharlesmusic.com

The downloads page distributes free audio plugins and software that Leslie has built. This is a functional distribution page, not a showcase. Visitors are musicians and audio engineers — they want to know immediately what the tool does, what platform it runs on, and how to get it.

---

## Data source

All entries live in `src/content/downloads.json`. Edit this file to add, update, or remove items. Never hardcode entries in the `.astro` file.

```json
[
  {
    "name": "Plugin Name",
    "slug": "plugin-name",
    "type": "Audio Plugin",
    "format": ["AU", "VST3"],
    "platform": ["macOS"],
    "arch": ["Apple Silicon", "Intel"],
    "version": "1.0.0",
    "updated": "2026-06-01",
    "description": "One or two sentences. What it does, not what it is.",
    "size": "14 MB",
    "file": "/downloads/plugins/plugin-name-v1.0.0.zip",
    "changelog": "https://github.com/yourrepo/releases/tag/v1.0.0",
    "category": "plugin"
  }
]
```

**Valid category values:** `"plugin"`, `"software"`
**Valid format values:** `"AU"`, `"VST3"`, `"AAX"`, `"Standalone"`
**Valid platform values:** `"macOS"`, `"Windows"`, `"Linux"`
**Valid arch values:** `"Apple Silicon"`, `"Intel"`, `"Universal"`

---

## File storage rules

- Files under ~20MB: store in `/public/downloads/plugins/` or `/public/downloads/software/` — Astro/Netlify serve them directly
- Files over ~20MB: host on Cloudflare R2 (free egress) and use the R2 public URL as the `file` value in the JSON — do not commit large binaries to the Git repo
- **Always version the filename:** `plugin-name-v1.0.0.zip` not `plugin-name.zip` — overwriting a file at the same URL silently breaks any existing links people have saved
- Never commit large binaries to Git — it permanently bloats the repository

---

## Component structure

```astro
---
import downloads from '../content/downloads.json';

const plugins = downloads.filter(d => d.category === 'plugin');
const software = downloads.filter(d => d.category === 'software');
---

<section>
  <h2>Plugins</h2>
  <ul class="downloads-list">
    {plugins.map(item => (
      <li class="download-item">
        <div class="download-info">
          <h3>{item.name}</h3>
          <p class="download-meta">
            {item.format.join(' / ')} · {item.platform.join(', ')} · {item.arch.join(', ')} · v{item.version}
          </p>
          <p class="download-desc">{item.description}</p>
          <p class="download-updated">Updated {item.updated}</p>
        </div>
        <div class="download-actions">
          <a href={item.file} download class="download-btn">
            Download — {item.size}
          </a>
          {item.changelog && (
            <a href={item.changelog} target="_blank" rel="noopener noreferrer" class="changelog-link">
              Changelog
            </a>
          )}
        </div>
      </li>
    ))}
  </ul>
</section>
```

---

## What to show per item

Every download entry must display all of the following before the download link. Visitors should never have to download something to find out if it works on their system:

- Name
- Format (AU / VST3 / etc.)
- Platform (macOS / Windows)
- Architecture (Apple Silicon / Intel / Universal) — critical for macOS users
- Version number
- Last updated date
- One or two sentence description of what it actually does
- File size
- Link to changelog if one exists

---

## Download link markup

```html
<a href="/downloads/plugins/plugin-name-v1.0.0.zip" download>
  Download — 14 MB
</a>
```

- The `download` attribute triggers a file save instead of the browser trying to open the file
- Always show file size in the link label — visitors on slow connections need to know
- For external files (Cloudflare R2), omit the `download` attribute and use `target="_blank" rel="noopener noreferrer"` instead — the `download` attribute only works for same-origin URLs

---

## CSS pattern

```css
.downloads-list {
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;
  flex-direction: column;
  gap: 2rem;
}

.download-item {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  padding-bottom: 2rem;
  border-bottom: 1px solid var(--color-border); /* use your token */
}

/* Side-by-side on wider screens */
@media (min-width: 768px) {
  .download-item {
    flex-direction: row;
    justify-content: space-between;
    align-items: flex-start;
  }

  .download-actions {
    flex-shrink: 0;
    text-align: right;
  }
}

.download-meta {
  font-size: 0.8rem;
  opacity: 0.6;
  font-family: var(--font-mono); /* use your token */
}
```

---

## What not to do

- Do not use a card grid layout — this is a list of tools, not a visual gallery. A vertical list with clear metadata is easier to scan for technical users
- Do not omit platform and architecture info — an Apple Silicon-only plugin that silently fails on Intel is a bad experience
- Do not use the same filename for different versions — always increment the version in the filename
- Do not stream or embed the plugins — there is nothing to preview, just download
- Do not store large files in the Git repo — use Cloudflare R2 or similar for anything over 20MB
