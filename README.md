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
- **Body** — Open Sauce Sans 400. Same family as the name, just unbolded, so the page
  reads as one voice at different weights rather than two competing typefaces.
- **Editorial** — Instrument Serif italic, class `.ed`. Reserved for short asides:
  *selected work*, *a trajectory*, *welcome to my corner of the internet*. Never a
  full sentence of body copy.
- **Technical** — IBM Plex Mono, class `.tech`. Only for genuine system data:
  `p = 0.700`, `p_c = 0.5`, phase labels, navigator track numbers, the micro-annotations,
  the `now viewing` indicator.
  Not for eyebrows, tags or UI labels — mono leaking into non-technical text is what
  makes a page read as terminal output instead of celestial editorial.

`MATHEMATICS × COMPUTING` and the three tags are sans at weight 600 with wide tracking,
which reads technical without the schematic personality.

The restraint is the point. If the italic serif starts showing up inside paragraphs
it stops reading as elegant.

---

## The mathematical sky

`#sky` is a fixed full-viewport canvas behind everything. It is not a star PNG.

- Two fields of points. The **top-left** field is dense at the corner and thins across
  the upper band. The **bottom-right** field mirrors it at about 74% brightness, so the
  corner has structure without competing.
- Edges come from a nearest-neighbour graph, so what reads as constellations is a
  proximity graph.
- A **dark spiral nebula** behind everything at the top right, drawn *darker* than the
  page tone (`rgba(9,12,32,.66)` core, two log-spiral arms) with a hairline of light on
  the outer edge so it reads as depth rather than a smudge.
- Three enormous background arcs (radii near the viewport diagonal) at
  `rgba(170,185,255,.035)` — you should almost not notice them.

> **A bug worth remembering.** `<canvas>` is a *replaced element*. `position:fixed;
> inset:0` does **not** stretch it — without explicit `width:100%; height:100%` it stays
> at its intrinsic 300×150px. The whole sky was being drawn into a small box in the
> top-left corner, which is exactly why the field looked like it stopped abruptly.
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

Three stages, no cuts, all driven by the single slider. The point is that each state
is *derived* from the previous one rather than swapped in for it.

**DISORDER — Stage 1, lattice.** Bond percolation on a 20×20 lattice. Each bond gets
a fixed random threshold at load and opens when `p` passes it, so the slider only ever
adds edges. Union-find tracks clusters live.

**CRITICALITY — Stage 2, spiral galaxy.** Criticality is *detected* from the cluster —
when the giant component passes 34% of the lattice (`SNAP`), one ripple propagates
outward — but the morph is *driven by `p`* across a wide band, `0.52 → 0.78`, smoothstepped.

This split matters. The percolation transition is genuinely sharp: `giantFrac` runs from
0.34 to 0.95 across roughly 0.12 of the slider. Tying the morph directly to cluster size
makes the galaxy flash past, so it reads as a jump straight to the final state. Spreading
it over a quarter of the slider gives a band you can actually sit inside and watch the
lattice reorganize. Stranded clusters drift outward and
fade. The connected sites migrate onto a **logarithmic spiral**, `r = ae^{bθ}`, in two
arms with per-node radial and angular scatter so it isn't sterile.

Two details that matter:

- Radius is chosen *first* and θ solved backwards as `θ = ln(r/a)/b`. Parameterising
  by θ instead piles most sites into the core, because `r` grows exponentially.
- Site ordering is the identity, so lattice neighbours land adjacent on the spiral and
  the edges trace the arms. With a scattered ordering the chords cross the whole galaxy
  and bury the structure.

It rotates **differentially** — angular velocity is `0.55 + 0.45(1 − r/R)`, so inner
sites turn faster and the arms wind, as they do in a real disc. Moving the pointer
across the canvas perturbs the whole galaxy's rotation by up to ±0.42 rad, eased.

**HARMONY — Stage 3, rings.** From `p = 0.88` to `1.0`, the arms resolve continuously
into concentric rings and pick up a rigid spin. This is where the music idea lives now:
an easter egg at the far end of the slider rather than the thesis of the page. Sites
are allotted per ring in proportion to circumference, and each ring is offset by the
golden angle so the dots never line up into spokes.

Position is a double interpolation:
`lerp(lerp(lattice, galaxy, t1), rings, t2)` — `t1` per-node, `t2` global and tied to `p`.

Knobs, near the top of section 2 of the script:

- `N = 20` — lattice size
- `SNAP = 0.34` — how large the giant component must get
- `ARMS = 2`, `TURNS = 2.6`, `BSPIRAL` — spiral geometry. If you change `TURNS`, change
  the divisor in `BSPIRAL` to match or the arm count and winding stop agreeing.
- Long chords are faded by length once the spiral forms (`lf` in the edge loop). Without
  it the cross-galaxy edges bury the arms and the object reads as a dense cluster rather
  than a spiral.
- `(p - 0.52)/0.26` — the band the galaxy morph is spread across
- `(p - 0.88)/0.12` — where the harmony stage begins
- `gphase += 0.0020` — galaxy rotation speed

The phase readout names the state; the scale under the slider lights the active zone.
The site opens itself after 15 seconds if nobody touches the slider.

**The bio is a consequence of interacting.** It has zero width until `p` reaches 0.42,
then expands. That fires *before* criticality on purpose — if the panel slide and the
galaxy morph happen on the same frame, both read as one confusing lurch.

**The explainer note.** A translucent rounded button under the bio shows `?` at rest and
widens to *what is this structure?* on hover. Clicking expands a centred panel explaining
the percolation transition and the logarithmic spiral. Markup is in `.note`; the toggle is
a few lines just above the router.

## Orbit navigator

Four satellites on the right edge, joined by a faint orbital path. At rest you see only
dots and their track numbers, collapsed hard against the edge.

> Labels are collapsed with `max-width:0; overflow:hidden`, **not** `opacity:0`. An
> invisible label still occupies layout width, which made the whole rail as wide as
> "EXPERIENCE" even at rest and pushed the dots inward over the hero copy.

Dots are pinned to the right edge (`order` puts the label and number ahead of the dot),
so labels expand leftward instead of shoving the rail around. Each dot carries a 5px ring
in the page colour so the orbital guide line appears to pass behind it. On hover the whole
rail gets a soft left-fading backdrop, which keeps labels readable where they cross the
tags. Active state is a larger filled dot with a double glow plus a brightened number. Hovering anywhere in the nav slides `01 ABOUT` … `04 RESUME` outward. The active
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
