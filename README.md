# clairejing.com

One self-contained `index.html`. No build step, no framework, no dependencies.

```
index.html    the entire site — styles, markup, and scripts inline
resume.pdf    shown in the RESUME tab and linked for download
photos/       (create this when you have images for the About page)
```

## Deploying

```bash
git add . && git commit -m 'redesign' && git push
```

First time: make a public repo named exactly `clairerjing.github.io`, push to `main`,
then Settings → Pages → Deploy from a branch → `main` → `/ (root)`.

For a custom domain: add a `CNAME` file containing your domain, point four `A`
records at `185.199.108.153`, `.109.153`, `.110.153`, `.111.153`, and a `CNAME`
for `www` at `clairerjing.github.io`.

---

## The design system

**Concept: mathematics in motion.** Percolation becomes a galaxy, networks become
constellations, data becomes geometry. Music arrives through harmony and frequency
rather than through music graphics.

### Colors

| Variable | Value | Role |
|---|---|---|
| `--base` | `#171c45` | deep indigo ground |
| `--deep` | `#0f1330` | recessed surfaces |
| `--glass` | `rgba(126,144,214,.07)` | translucent panels |
| `--ivory` | `#f2f4fb` | primary text |
| `--lav` | `#a3abcf` | secondary, cool gray-lavender |
| `--peri` | `#8fa5e8` | icy periwinkle accent |
| `--hi` | `#dde6ff` | almost-white blue highlight |
| `--line` | `rgba(210,220,255,.15)` | hairlines |
| `--glow` | `rgba(150,170,255,.18)` | soft glows |

The body background is three stacked radial gradients over `--base`, fixed so it
doesn't scroll. Deliberately no bright purple neon — celestial, not cyberpunk.

### Typography — three registers, no more

- **Display** — Open Sauce Sans 800, tight tracking. Names, page titles.
- **Body** — Source Sans 3. Paragraphs only.
- **Editorial** — Instrument Serif italic, class `.ed`. Reserved for short asides:
  *selected work*, *a trajectory*, *welcome to my corner of the internet*. Never a
  full sentence of body copy.
- **Technical** — IBM Plex Mono, class `.tech`. Only for real technical strings:
  `p = 0.560`, track numbers, tags, phase labels.

The restraint is the point. If the italic serif starts showing up inside paragraphs
it stops reading as elegant.

---

## The mathematical sky

`#sky` is a fixed full-viewport canvas behind everything. It is not a star PNG.

- Three depth layers of points, ~110 total.
- Edges come from a **nearest-neighbour graph** — each point connects to its two
  closest neighbours within 190px. What reads as constellations is a proximity graph.
- **Parallax:** each layer shifts against pointer movement in proportion to its depth.
- **Gravity:** points within 150px of the cursor drift toward it by up to 5px, and
  edges within 210px brighten from 3.5% to about 33% opacity. Everything relaxes when
  the pointer leaves.

Tuning knobs sit at the top of the `sky()` function: `LAYERS` for count and depth,
`190` for edge distance, `150` and `210` for the gravity and brightening radii.

---

## The homepage: one continuous system

Three stages, no cuts. The point is that the record is *derived* from the mathematics
rather than swapped in for it.

**Stage 1 — lattice.** Bond percolation on a 20×20 lattice. Each bond gets a fixed
random threshold at load and opens when `p` passes it, so the slider only ever adds
edges. Union-find tracks clusters live. Below threshold: scattered clusters.

**Stage 2 — critical.** When the giant component passes 34% of the lattice (`SNAP`),
a single ripple propagates outward — one ring, no confetti. Stranded clusters drift
outward and fade. The connected sites migrate onto a **Lissajous curve**,
`x = sin(3t + π/2)`, `y = sin(2t)`. Their edges stretch with them, so the graph
becomes a luminous harmonic figure. It rotates slowly.

**Stage 3 — grooves.** After the orbit holds for 2.6 seconds, the harmonic figure
winds into concentric grooves and the rotation speeds up. Sites are allotted per
groove in proportion to circumference so density stays even, and each groove is
offset by the golden angle so the dots never line up into spokes. Edges fade out and
the grooves carry the texture.

Position is a double interpolation:
`lerp(lerp(lattice, harmonic, t1), grooves, t2)` — `t1` per-node, `t2` global.

Knobs, all near the top of section 2 of the script:

- `N = 20` — lattice size
- `SNAP = 0.34` — how large the giant component must get
- `2600` in `tick()` — how long the orbit holds before winding in
- `GROOVES = 13`
- `spin += 0.0016 + 0.0034*t2` — rotation, faster once it's a record

The phase readout under the slider names the current state. The site opens itself
after 15 seconds if nobody touches the slider.

**The bio is a consequence of interacting.** It has zero width until the threshold is
crossed, then expands. That's why the reveal reads as *the system changed and the
interface exposed another layer* rather than *here's room for text*.

---

## Orbit navigator

Four satellites on the right edge, joined by a faint orbital path. At rest you see only
dots. Hovering anywhere in the nav slides `01 ABOUT` … `04 RESUME` outward. The active
page is a larger, brighter planet with a double glow.

Adding a section: add a `<button class="sat" data-go="x">` block to `#orbit` and a
matching `<section class="page" id="pg-x">`. Nothing else to wire.

The footer carries `now viewing — <page>`, which updates on navigation.

---

## Projects constellation

`#constel` is a hand-placed SVG. Each project is a node with a halo, a core, a name,
and a description that fades in on hover. Coordinates live in a `0 0 900 460` viewBox,
so moving a project means editing its `cx`/`cy`, the `x`/`y` of its two `<text>`
elements, and any `<line class="edge">` that touches it.

Watch the right edge — labels that extend too far slide under the navigator.

---

## Editing content

- **About text** — `#pg-about`, inside `.abouttext`.
- **Photos** — four squares under the skills staff. Replace a `<div class="ph"></div>`
  with `<img src="photos/one.jpg" alt="">`; the CSS crops to square.
- **Skills staff** — five `<text>` elements and five noteheads. Five skills, five lines.
- **Experience** — duplicate an `<li class="ev">`. Drop the `todo` class once it's real.

---

## Deliberately not built yet

These were in the brief and are real work, not oversights:

1. **Shared-element page transitions** — the homepage constellation collapsing, specific
   nodes detaching and becoming the project map. Needs GSAP or the View Transitions API
   plus a single persistent canvas shared across routes, which means restructuring the
   router.
2. **Live per-project visualizations** — an eigenvalue spectrum that spreads on hover, a
   GNN pulsing along its edges, a Kalman estimate tracking observations. Each is roughly
   a day of work, and they're the strongest thing you could add next.
3. **Magnetic links, scroll-drawn geometry, coordinate cursor readout.**
4. **Audio easter egg** — opt-in only, never autoplay.
