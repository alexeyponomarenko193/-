---
name: p5-brush
description: Write generative-art code with p5.brush (v2.x), the natural-media drawing library for p5.js and plain WebGL2 — pencils, charcoal, markers, watercolor fills, hatching, custom brushes and vector (flow) fields that bend strokes organically. Use when the user mentions p5.brush or brush.js, or wants hand-drawn, painterly, sketchy, watercolor or plotter-like strokes in p5.js / canvas sketches, generative art, or high-resolution print work.
---

# p5.brush

p5.brush (MIT, by Alejandro Campos Uribe — github.com/acamposuribe/p5.brush) adds natural drawing tools to p5.js: pencils, charcoal, markers, watercolor fills, hatch patterns, and vector fields.

## First: identify the build

The library ships in two incompatible builds. Decide which one before writing any code; mixing their APIs breaks silently.

| | p5 build | Standalone build |
|---|---|---|
| File | `dist/p5.brush.js` | `dist/brush.js` / `dist/brush.esm.js` |
| Needs | p5.js **2.x**, `createCanvas(w, h, WEBGL)` | Nothing but WebGL2; `brush.createCanvas(w, h)` |
| Transforms, angles, seeding | p5's own `push/translate/angleMode/randomSeed` | `brush.push/translate/angleMode/seed` |
| Each frame | Automatic | **Must end with `brush.render()`** |
| Clear | `background()` | `brush.clear(color?)` |

If the project already loads p5.js, use the p5 build. If there is no p5 and the user doesn't want it, use the standalone build. If it's unclear, ask.

CDN for the p5 build:

```html
<script src="https://cdn.jsdelivr.net/npm/p5@2.2/lib/p5.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/p5.brush@2.2.4"></script>
```

ESM: `import * as brush from 'p5.brush'` (p5 build) or `import * as brush from 'p5.brush/standalone'` (standalone build).

## Reference — read before writing code

- `reference/api.md` — compact, LLM-oriented API reference for both builds (setup, strokes, fills, hatches, fields, primitives, custom brushes, classes, gotchas). **Always read this first.**
- `reference/p5-build.md` — the full README with detailed function docs and examples for the p5 build.
- `reference/standalone.md` — full docs for the standalone build.

Look up exact signatures there instead of guessing; this API differs from vanilla p5 in many small ways.

## Gotchas that break code most often

- The WebGL origin is the canvas **center**. Translate by `(-width/2, -height/2)` to work in top-left coordinates.
- p5 build: canvas must be `WEBGL`, and p5 must be 2.x. Image-tip brushes need an `async setup()` that `await`s `brush.add(...)`.
- Standalone: forget `brush.render()` and nothing appears. Use `brush.*` transforms, `brush.angleMode`, `brush.seed`/`brush.noiseSeed`, never the p5 ones.
- `brush.arc()` is stroke-only; `brush.circle()`, `brush.rect()` and other shapes take stroke, fill and hatch.
- `brush.rect()` mode takes the strings `"corner"` / `"center"`, not p5 constants.
- Fill opacity is 0–255, not 0–100.
- Draw text or overlays after brush geometry if they must stay on top.

## Working well with it

- Seed randomness (`randomSeed`/`noiseSeed` or `brush.seed`) so pieces are reproducible, and expose the seed.
- For print, work at the final pixel size (or use `pixelDensity`) rather than scaling a small canvas up.
- Prefer the main primitives (`line`, `rect`, `circle`, `polygon`, `spline`, `flowLine`…) for visible shapes; reach for `brush.Polygon` / `brush.Plot` only for stored geometry.
- Custom brushes and flow fields can be designed visually in the author's Brush Maker and Flow Field Generator (linked in `reference/p5-build.md`), which output ready-to-paste `brush.add()` / `brush.addField()` code.
