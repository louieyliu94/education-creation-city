# The River — AI Skill
**Project:** education-creation-city · `index.html`
**Scope:** This skill applies only to this project. Do not carry these rules into other projects.

---

## What This Project Is

The River is a single-file interactive portfolio. Projects are **stone islands** in a watercolour river that flows diagonally from the bottom-left to the top-right of the screen. The river is the portfolio — it grows wider and longer as more projects are added. There is no sidebar, no grid, no navigation menu. Discovery is the interface.

The aesthetic is dry-brush watercolour in the tradition of J.M.W. Turner and Annie Pootoogook: frayed, textured, atmospheric. The mood is cool, misty, predawn — quietly hopeful. Nothing should feel digital or mechanical.

All code, styles, and content live in **one file: `index.html`**. Binary assets (images) are base64-inlined at commit time so the file is fully self-contained.

---

## Architecture

The canvas is a full-viewport SVG (`viewBox="0 0 1400 900"`). Four stacked `<g>` layers are painted in this order:

| Layer | Contents |
|---|---|
| `#river-layer` | River body spline paths + animated frayed flow strokes |
| `#nature-layer` | Reeds, iris marks, bank pebbles, ripple rings, floating debris |
| `#island-layer` | Sand/silt island ellipses, lichen tufts, mist |
| `#card-layer` | `<foreignObject>` stone cards with 3D flip |

The river spine is a **Catmull-Rom spline** through all island waypoints. Tapering is achieved by stacking stroke segments of decreasing width from each project outward to the destination — no manual geometry needed. Adding a project to the `PROJECTS` array automatically extends and widens the river.

Stone cards use CSS `perspective` + `rotateY(180deg)` triggered by `.card-wrap:hover`. Each card back and front is clipped to a unique organic stone silhouette via `clip-path: path(...)` on `[data-stone="N"]`. The wobble effect is SVG SMIL `animateTransform` rotating the card's parent `<g>` around a pivot at the stone's base.

The modal supports two modes: a **single-page** view (cover image + text) and a **paginated** view (up to 6 illustrated pages with ← dot-nav →), activated by the presence of a `pages` array on the project object.

Scroll and two-finger trackpad swipe zoom the SVG up to 150% (1.5×), centred on the cursor, via a `wheel` listener on `#explorer`.

---

## Colour & Style

The palette is intentional and restrained. Treat it as fixed unless the user explicitly asks to change it.

| Role | Value | Notes |
|---|---|---|
| Page background / SVG base | `#c8cdd4` | Cool blue-grey — the ground everything sits on |
| River body (deep) | `#6898b8` | Medium steel blue |
| River body (highlight) | `#80aac8` | Lighter layer on top |
| Animated flow strokes | `#1c3a8a` | Deep ultramarine — the most saturated blue in the scene |
| Iris bank marks | `#2840a0` | Ultramarine, slightly lighter than flow strokes |
| Stone card backs | `#9aaebe` | Wet-stone blue-grey |
| Island sand | `#beba9a` | Warm buff — the only warm neutral in the water |
| Reed stems / lichen | `#7a8a60` | Muted olive green |
| Dawn glow (top-right) | `#dccca0` | Warm amber — light source, used sparingly |
| Modal surface | `rgba(246,244,240,0.96)` | Frosted warm white |
| UI accents / buttons | `#2840a0` | Matches iris marks |

**Deep ultramarine (`#1c3a8a`) belongs only to the animated flow strokes and bank iris marks.** It should not appear in backgrounds, cards, or decorative elements — its scarcity is what makes it feel like moving water rather than decoration.

The watercolour effect comes from layering low-opacity strokes and ellipses with blur filters, never from solid fills. When adding visual elements, prefer opacity between 0.06 and 0.45. Hard edges and full opacity should be rare.

---

## Design Constraints

- **No hierarchy between projects.** All stones are the same size, same interaction, same visual weight. The river position is the only ordering.
- **Equal visual weight across all 10 projects.** Do not make one island larger, brighter, or more prominent than another.
- **The river is made of projects.** The river body is generated from the project positions — do not add decorative river geometry that isn't derived from the `PROJECTS` array.
- **Single-file discipline.** Keep everything in `index.html` unless a binary file (image, font) makes it genuinely impossible.
