# clairejing.com

One self-contained `index.html`. No build step, no framework, no dependencies.
Open it in a browser and it works; put it on any host and it works.

```
index.html    the entire site — styles, markup, and scripts inline
resume.pdf    shown in the RESUME tab and linked for download
logos/        (create this later if you want company logos)
```

## Putting it online with GitHub Pages

1. Make a new **public** repo named exactly `clairerjing.github.io`.
   The name matters — GitHub treats `username.github.io` as your personal site.

2. From this folder:

   ```bash
   git init
   git add .
   git commit -m "first version"
   git branch -M main
   git remote add origin https://github.com/clairerjing/clairerjing.github.io.git
   git push -u origin main
   ```

3. Repo → Settings → Pages → Source: **Deploy from a branch**, branch `main`, folder `/ (root)`.

4. Live at `https://clairerjing.github.io` in a minute or two.

To update: edit `index.html`, then `git add . && git commit -m "..." && git push`.

**Faster alternative:** drag this folder onto [netlify.com/drop](https://app.netlify.com/drop).
Live in about ten seconds, no git required.

## Custom domain

1. Add a file named `CNAME` containing just your domain, e.g. `clairejing.com`
2. At your registrar, add:
   - Four `A` records for `@` → `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`
   - One `CNAME` for `www` → `clairerjing.github.io`
3. Repo → Settings → Pages → Custom domain → enter it → tick **Enforce HTTPS**

---

## Fonts

Currently **Open Sauce Sans** for headings and **Arimo** for body text — two of the
faces from your list, both legally servable from a CDN.

The others you named are proprietary and have no legal web CDN:

| Font | Status |
|---|---|
| Open Sauce Sans | Open (OFL) — loaded from Fontsource |
| Arimo | Open (Apache) — loaded from Google Fonts |
| Canva Sans | Licensed to Canva. Not available as a webfont. |
| Arial MT Pro | Monotype commercial licence required. |
| Elika Gorica | Commercial licence required. |
| Libre Baskerville | Open (OFL) — available if you want to swap it in |

**If you own a licence** for any of the proprietary ones, self-hosting is easy.
Convert the file to `.woff2` (transfonter.org does this free), drop it in a
`fonts/` folder, and add this above the `:root` block:

```css
@font-face{
  font-family:'Elika Gorica';
  src:url('fonts/elika-gorica.woff2') format('woff2');
  font-weight:700; font-display:swap;
}
```

Then change one line in `:root`:

```css
--head:'Elika Gorica','Open Sauce Sans',sans-serif;
```

`--head` drives every heading, `--body` drives every paragraph. Nothing else to touch.

## Colors

All in the `:root` block at the top. `--vio*` are the purples, `--pnk*` the pinks,
`--w*` the whites. Change those six values and the whole site follows, including
the waveform gradient and the percolation graph.

## The homepage visualization

Bond percolation on a 22×22 lattice. Each bond gets a random threshold once at
load, and is open when `p` passes it — so dragging the slider only ever *adds*
edges, and the picture evolves smoothly instead of re-randomizing. Union-find
tracks the clusters live.

When the largest cluster passes 34% of the lattice (`SNAP` in the script), its
nodes migrate onto a ring. The mapping is deliberate: lattice site `i` goes to
ring slot `i × 283 mod 484`, and 283/484 is within a rounding error of 7/12 —
so **every lattice bond becomes a perfect fifth**, and the chords trace out the
circle of fifths rather than just decorating it.

Knobs worth knowing, all near the top of the script:

- `N = 22` — lattice size. Higher is denser and slower.
- `SNAP = 0.34` — how big the giant component must get before the ring forms.
- The slider starts at `p = 0.18` so visitors land on scattered clusters rather
  than a dead grid.

If nobody touches the slider, the site opens itself after 14 seconds so a
recruiter in a hurry is never stuck at the toy.

## Editing content

- **About text** — `#pg-about`, inside `.abouttext`.
- **Skills staff** — the five `<text>` elements in the `.staff` SVG, plus the
  matching noteheads. Five skills, five staff lines. Adding a sixth means
  moving the note positions.
- **Experience** — `#pg-experience`. Duplicate an `<li class="ev">` to add an
  entry. The last one is marked `class="ev todo"`, which greys it out; remove
  `todo` once it's real.
- **Projects** — currently a placeholder block. Ready when you are.
- **Adding a nav tab** — add a `<button data-go="x">` in `#navbtns` and a
  `<section class="page" id="pg-x">`. The waveform generates one packet per
  button automatically, so the wave reshapes itself.

## Notes

- Your phone number is on the site because you asked for the phone icon. Worth
  knowing: published numbers get scraped for spam calls. Deleting the last
  `<a>` in `#contact` removes it.
- Everything respects `prefers-reduced-motion` — the morph, the playhead, and
  the page transitions all go instant.
- The RESUME tab embeds `resume.pdf` in an iframe. Some mobile browsers refuse
  to render PDFs inline, which is why the download and new-tab buttons sit
  above it.
