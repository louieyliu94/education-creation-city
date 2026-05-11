# The River — Project Explorer
## Trae AI Skill File

This is a single-file interactive portfolio called **The River**. All logic, styles, and content live in `index.html`. The `assets/` folder holds binary files (images) that are base64-inlined into the HTML at commit time.

---

## Concept & Design Philosophy

The River is a living, growing portfolio visualisation. Projects are **stone islands** sitting in a river that flows **bottom-left → top-right**. The aesthetic is dry-brush watercolour in the style of J.M.W. Turner and Annie Pootoogook — cool, misty, predawn, hopeful.

Core principles:
- **No hierarchy.** All projects carry equal visual weight. Position along the river is the only implicit ordering (newest feel freshest at the source).
- **The river grows with the work.** Adding a project to the `PROJECTS` array automatically widens and extends the river. No manual layout.
- **Interaction is discovery.** Stones lie face-down; hover flips to reveal the project cover; click opens the detail modal.
- **Colour discipline.** Deep ultramarine/Burberry blue (`#1c3a8a`) is reserved for the animated flow strokes and iris bank marks only — never scattered freely. The river body uses steel blues (`#6898b8`, `#80aac8`). The overall palette is cool grey (`#c8cdd4`) with warm dawn glow top-right.

---

## File Structure

```
index.html          — everything: CSS, SVG scaffold, JS, project data
assets/
  sisyphus.webp     — source image for project 1 page 1 (also base64-inlined in HTML)
CLAUDE_CODE.md      — Claude Code feature reference
skill.md            — this file
```

---

## SVG Architecture

The canvas is a full-viewport SVG (`viewBox="0 0 1400 900"`, `preserveAspectRatio="xMidYMid meet"`).

Four stacked `<g>` layers, in paint order:

| Layer id | Contents |
|---|---|
| `#river-layer` | River body paths, frayed animated flow strokes |
| `#nature-layer` | Reeds, iris marks, bank pebbles, ripple rings, floating debris |
| `#island-layer` | Sand/silt island ellipses, lichen tufts, mist spray |
| `#card-layer` | `<foreignObject>` wrappers containing the HTML stone cards |

Key coordinate constants:
```js
const SRC  = { x: 24,   y: 916 };   // river source (bottom-left, off-canvas)
const DEST = { x: 1388, y: 8   };   // river destination (top-right, off-canvas)

// Perpendicular unit vectors to the river direction vector (1364, -908):
const LX = -0.554, LY = -0.832;     // upper-left bank direction
const RX =  0.554, RY =  0.832;     // lower-right bank direction
```

---

## Adding a New Project

Append an object to the `PROJECTS` array. The river auto-widens.

```js
{
  id: 11,               // increment from last
  ix: 1360, iy: 52,     // island centre in SVG coordinates
                        // follow the diagonal from (128,838) toward (1284,96)
  title: "Project Name",
  tag: "Theme & Discipline",
  tagBg: "#hex", tagFg: "#hex",   // pill background and text colour
  desc: "One-sentence description shown in single-page modal.",
  body: "Longer paragraph or pull quote.",

  // Optional: 6-page portfolio view (see project 1 for full example)
  pages: [
    {
      heading: "Page Title",
      caption: "Photo caption text",
      body: "Body paragraph for this page.",
      photo(w, h) { return `<svg ...>...</svg>`; }
      // or: photo(w, h) { return `<img src="data:image/...;base64,...">`; }
    },
    // ... up to 6 pages
  ],

  // Stone card flip face (shown at 70×94 px, clipped to stone silhouette)
  makeCover(w, h) {
    return `<svg viewBox="0 0 ${w} ${h}" xmlns="http://www.w3.org/2000/svg">
      <!-- your cover art here -->
    </svg>`;
  }
}
```

**Island coordinate guide** — rough diagonal spacing between projects:
```
id:1  → (128, 838)   id:6  → (794, 424)
id:2  → (302, 752)   id:7  → (870, 338)
id:3  → (378, 664)   id:8  → (1040, 260)
id:4  → (548, 588)   id:9  → (1116, 174)
id:5  → (626, 502)   id:10 → (1284, 96)
```
New projects beyond 10 should continue the ~(+155, -85) step pattern.

---

## River Construction (`buildRiver`)

The river is built from a **Catmull-Rom spline** through all island waypoints plus SRC and DEST.

Tapering is achieved by stacking overlapping stroke segments — each segment starts from project *i* and runs to DEST with width `(N−i)×26 + 18`. The result: narrow at the source, wide at the destination.

Animated flow uses six **frayed dashed strokes** offset slightly from the spine, each with a different `stroke-dasharray` and CSS animation class (`rf-a` through `rf-d`) cycling `stroke-dashoffset`. Colour: deep ultramarine `#1c3a8a`.

```css
@keyframes rf { to { stroke-dashoffset: -500; } }
.rf-a { animation: rf  5s linear infinite; }
.rf-b { animation: rf  8s linear infinite; }
.rf-c { animation: rf 13s linear infinite; }
.rf-d { animation: rf 20s linear infinite; }
```

---

## Stone Cards

Cards are `<foreignObject>` elements inside an SVG `<g>` that carries a SMIL `animateTransform` (gentle rotation wobble, pivot at stone base).

- Face-down (stone back) by default; CSS `.card-wrap:hover .card-inner` flips to project cover via `rotateY(180deg)`.
- Click fires `openDetail(id)` → modal opens.
- Each stone has a unique organic `clip-path: path(...)` defined in CSS via `[data-stone="N"]` selectors (stones 1–10).
- Card size: **70 × 94 px**. `foreignObject` is 98 × 124 px to give perspective transform room.

---

## Modal System

`openDetail(id)` checks whether the project has a `pages` array:

- **With `pages`**: adds `.paged` class to `#modal-card`, renders paginated view with ← dot-nav → controls. Each page shows `photo(420, 255)`, a label, caption, and body text.
- **Without `pages`**: single-page view — `makeCover(420, 560)` + title + tag + desc + body + explore button.

---

## Scroll-to-Zoom

Mouse scroll (or two-finger swipe on Mac) zooms the SVG up to **50 %** (`ZOOM_MAX = 1.50`), centred on the cursor position. Implemented as a `wheel` event listener on `#explorer` that sets `transform: scale(n)` and `transform-origin` on `#river-svg`.

---

## Natural Scene Elements (`buildNature`)

Between each consecutive waypoint pair, `buildNature` places:
- **Reeds** (upper-left bank) — `placeReeds(layer, ux, uy, seed)` — cattail stems with leaf blades
- **Iris marks** (lower-right bank) — `placeIris(layer, lx, ly, seed)` — ultramarine petal strokes
- **Bank pebbles** — `placeBankPebbles(layer, cx, cy, seed)` — warm grey ellipses
- **Ripple rings** — SMIL-animated expanding ellipses around each island
- **Floating leaf debris** — SMIL-animated small ellipses drifting downstream

Bank offsets: `bankDist = 48 + i × 8` (grows as river widens).

---

## Colour Reference

| Role | Hex |
|---|---|
| Page background / SVG base | `#c8cdd4` |
| River body (deep layer) | `#6898b8` |
| River body (highlight layer) | `#80aac8` |
| Animated flow strokes | `#1c3a8a` (ultramarine) |
| Iris bank marks | `#2840a0` |
| Stone card back | `#9aaebe` |
| Island sand | `#beba9a` |
| Reed stems | `#7a8a60` |
| Dawn glow (top-right) | `#dccca0` |
| Modal background | `rgba(246,244,240,0.96)` |
| Explore button / accents | `#2840a0` |

---

## What NOT to Do

- Do not add ultramarine/blue marks anywhere except the bank positions and flow strokes.
- Do not introduce visual hierarchy between projects (no size/opacity differences between stones).
- Do not break the single-file architecture — keep everything in `index.html` unless binary assets require a separate file.
- Do not add comments explaining what code does — only add a comment if the *why* is non-obvious.
- When adding a project's `makeCover` SVG, use gradient IDs `c{id}` (e.g. `c11`) to avoid conflicts with the other inline SVGs already on the page.
