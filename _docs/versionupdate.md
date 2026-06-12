# Version Update — Visual Redesign Workflow

## Stack
Current site is plain HTML / CSS / vanilla JS, deployed from `main` branch.

## Workflow

1. **Decide the new look** — update `_docs/LookandFeel.md` with direction (fonts, colours, layout)
2. **Create `v2` branch** — live site on `main` stays untouched
3. **Edit `style.css` + HTML files** — preview locally and via branch preview URL
4. **When happy** — merge `v2` into `main`, live site updates instantly

## Branch preview
Netlify/Vercel deploys every branch to its own temporary URL (e.g. `v2--lesliecharlesmusic.netlify.app`) so the redesign can be reviewed live before touching the real domain.

## Files to edit
- `style.css` — almost all visual changes
- `index.html`, `about.html`, `discography.html`, `downloads.html`, `workshops.html`, `contact.html` — structural/layout changes
- `Artwork/`, `Photos/` — swap or add images as needed

## Local preview
```bash
python3 -m http.server
```
Then open `http://localhost:8000` in a browser. No build step needed.
