# Fixed Wave

**A seed-based generative system for animated wave-equation compositions.**

A catalogue of computational textile compositions for fashion, textile and surface design — algorithmically drawn, seed-documented, and ready for production.

---

## Overview

Fixed Wave is a generative design system rather than a single artwork. Each composition is a small grid of cells, one of which is a continuously oscillating source. The wave propagates outward from the source, reflecting off the fixed boundaries of the frame, until the grid fills with a slow, breathing pattern of interference.

The system is designed for:

- **Fashion houses** adapting physical ornament for apparel and accessories
- **Textile studios** developing repeat patterns and yardage
- **Surface designers** working across print, wallpaper, and interior applications

Every composition can be licensed, adapted, or commissioned to a brief.

---

## Concept

A wave, when it is *simulated* rather than drawn, becomes a physics — rippling, precise, quietly yours.

The wave — layered, oscillating, endlessly variable — has always carried the structure of ornament. From the geometric ripples of a Persian garden pool to the wave equation that governs sound and light, the physics of the wave is one of the oldest systems of mathematical beauty we have. Fixed Wave translates the wave equation into code. Each composition begins with an empty grid and unfolds through a finite-difference simulation driven by a single oscillating source, until the field fills with a slow pattern of interference.

The grid size, the wave speed, the source frequency, the source position, and the two-colour gradient palette are all derived from a single numeric seed.

Unlike most of the still volumes in this series, **Fixed Wave's plate is animated.** The wave equation needs to advance in time; the whole point of the composition is the slow ripple of the field. But the framed plate, the surfaces, the archive thumbnails, and the cover are all rendered as single static frames — the frame you would print.

---

## Features

- **Seed-based generation** — every composition is defined by a numeric seed and can be regenerated exactly
- **Deterministic output** — the same seed always produces the same composition at any given frame
- **Wave-equation simulation** — a finite-difference solution of the 2D wave equation, updated one step per frame
- **Oscillating source** — a single cell in the grid is driven by a sine function at a seed-defined frequency
- **Fixed boundaries** — the four edges of the grid are pinned to zero, producing the reflection that gives the composition its classic ripple pattern
- **Two-colour gradient** — the field is rendered by mapping each cell's amplitude into a linear gradient between two named colours
- **Sixteen-colour palette** — the palette is chosen from a fixed set of 16 named colours (Red, Blue, Cyan, Magenta, Olive, Beige, etc.)
- **Live animated plate** — the wave ripples continuously as the simulation advances
- **Static catalogue** — the framed plate, surfaces, and archive thumbnails are frozen single frames
- **Adaptive surfaces** — one seed applied across print, scarf, textile, and wall formats
- **Archive** — eight curated seeds available for immediate loading
- **Download** — export the current frame as a high-resolution PNG
- **Keyboard shortcuts** — `R` for new seed, `S` to save

---

## Project Structure

```
.
├── index.html          # Main catalogue page
├── images/
│   ├── fav.svg         # Favicon
│   ├── tote.png        # Mockup: tote bag
│   ├── tee.png         # Mockup: t-shirt
│   └── cushion.png     # Mockup: cushion
└── README.md
```

---

## How It Works

### The Seed

A numeric seed (a large integer) initializes a deterministic pseudo-random generator. From this seed, the system derives:

- Start colour (from a palette of 16 named colours)
- End colour (a different colour from the same palette)
- Grid columns (10 to 160)
- Grid rows (columns − 5 to columns + 5)
- Time step `dt` (0.005 to 0.015)
- Wave speed `c` (scaled to the canvas)
- Source frequency (10 to 310)
- Source position (any interior cell of the grid)

Because the generator is deterministic, the same seed always produces the same composition — on any device, at any time.

### The Wave Equation

Fixed Wave solves the 2D wave equation by finite differences:

```
∂²u/∂t² = c² (∂²u/∂x² + ∂²u/∂y²)
```

At every step, each interior cell is updated from its previous value and the values of its four neighbours:

```
next[i][j] = 2 · current[i][j] − prev[i][j]
           + coeff · (
               (current[i+1][j] + current[i−1][j] − 2·current[i][j]) / dx² +
               (current[i][j+1] + current[i][j−1] − 2·current[i][j]) / dy²
             )
```

where `coeff = c² · dt²`. This is the classic explicit scheme for the wave equation: cheap to compute, and stable as long as `c · dt` is small enough.

### The Source

A single cell of the grid — the "oscillating point" — is driven by a sine function on each step:

```
current[p.i][p.j] += sin(p.time · p.freq · 2π) · 450
```

This forces the source cell to move up and down over time. The energy from this motion propagates outward through the grid, reflecting off the fixed boundaries, and settling into a stable standing-wave pattern.

### The Boundaries

The four edges of the grid — the first and last row, and the first and last column — are pinned to zero on every step. This produces a "fixed" or "Dirichlet" boundary condition: waves that reach the edge reflect back inward. The boundaries are what give Fixed Wave its characteristic rippled, mirror-symmetric composition.

### The Colour

Each cell's amplitude is mapped into a linear gradient between two named colours:

| Field value       | Rendering                              |
|-------------------|----------------------------------------|
| −450 (negative)   | Start colour, low alpha                |
| 0 (rest)          | Midpoint of the gradient, mid alpha    |
| +450 (positive)   | End colour, high alpha                 |

The alpha channel also scales with the amplitude, so the wave reads as a soft, luminous field — the rest state fades into the background, and only the peaks stand out.

### The Animation

Fixed Wave is one of the animated volumes in this series. The live plate:

- **Advances the wave** by one simulation step per frame
- **Redraws** the whole grid with the updated field
- **Honors reduced motion** — if `prefers-reduced-motion: reduce`, the plate renders a single frame and does not animate
- **Pauses when hidden** — when the tab is not visible, animation stops to save CPU
- **Freezes for download** — when you click Download, the animation pauses, the current frame is captured, then the animation resumes

### The Surfaces

The same seed is rendered across four surface formats. These are static frames — they represent the print-ready composition.

| Surface  | Aspect | Material          |
|----------|--------|-------------------|
| Print    | 1 : 1  | Cotton rag        |
| Scarf    | 3 : 1  | Twill silk        |
| Textile  | 4 : 3  | Fabric yardage    |
| Wall     | 2 : 3  | Wallpaper         |

Each surface uses the same underlying seed and structural logic — only the repeat, orientation, and scale change.

### A Note on Warm-Up

The wave equation starts with every cell at zero. Without warm-up, the static surfaces would render as a flat empty field. So each static canvas runs **60 simulation steps before drawing**, giving the wave time to propagate and settle into a visible pattern. The live plate runs 30 warm-up steps on init, and then continues advancing frame by frame.

---

## Usage

### In the browser

1. Open `index.html` in any modern browser.
2. The Plate 001 composition ripples on its own.
3. Click **New Seed** to generate a new composition.
4. Click **Download** to save the current frame as a PNG.
5. Scroll to the **Archive** section and click any plate to load it into Plate 001.

### Keyboard shortcuts

| Key | Action          |
|-----|-----------------|
| `R` | New seed        |
| `S` | Save as PNG     |

### Reproducing a composition

Each composition is identified by an 8-digit seed label displayed in the metadata panel. To reproduce a specific composition statically, note the seed and regenerate it programmatically:

```js
const rng = new RandomGenerator(seed);
const features = buildFeatures(rng, canvasSize);
// Warm up the wave
const rectWidth = (canvasSize * features.scaleFactor) / features.cols;
const rectHeight = (canvasSize * features.scaleFactor) / features.rows;
for (let i = 0; i < 60; i++) updateWave(features, rectWidth, rectHeight);
renderComposition(canvas, features, false);
```

For an animated reproduction, use the same pattern with a `requestAnimationFrame` loop and call `updateWave` once per frame before redrawing.

---

## Technical Notes

- **No build step.** The system is a single HTML file with inline CSS and JavaScript.
- **No dependencies.** All drawing is done with the native Canvas 2D API.
- **Deterministic.** The `RandomGenerator` class uses a xorshift-based PRNG seeded by an integer, so identical seeds produce identical outputs.
- **Animation isolated to the plate.** Only the live Plate 001 uses `requestAnimationFrame`. All other canvases render a single frame and stop.
- **Feature isolation.** Cover, framed plate, surfaces, and archive thumbnails each derive their own feature set from their own local RNG, without disturbing the main plate's state.
- **Stable simulation.** The wave-speed / time-step ratio is bounded so the finite-difference scheme stays stable. No runaway oscillation.
- **Fixed boundaries.** The four edges of the grid are pinned to zero on every step, producing the reflection that gives Fixed Wave its classic interference pattern.
- **Warm-up on static renders.** The framed plate, surfaces, archive thumbnails, and cover all run 60 simulation steps before drawing, so the wave is visible in the first frame.
- **Cleanup on visibility change.** Animation pauses when the tab is hidden, and restarts cleanly when it returns.
- **Responsive.** The layout adapts from large desktop down to very small mobile devices (tested at 360px viewport width).
- **Accessible.** Supports `prefers-reduced-motion`. Pinch-zoom is enabled.

### Browser support

Tested in current versions of:

- Chrome / Edge
- Firefox
- Safari (desktop and iOS)

---

## Licensing

All Fixed Wave compositions are **seed-documented** and available for licensing across textile, surface, and print applications.

- **Standard licenses** cover single-product production runs.
- **Commercial use, custom editions, or exclusive rights** are available on request.

Each license is issued against a specific seed ID. Regeneration of the same seed produces the identical composition — ensuring reproducibility between artist, studio, and manufacturer.

For licensing enquiries: [reyhanehdaneshdoost@gmail.com](mailto:reyhanehdaneshdoost@gmail.com)

---

## Commission

Fixed Wave is a generative design system, not a fixed artwork. It can be adapted for specific briefs:

| Service     | Description                                                       |
|-------------|-------------------------------------------------------------------|
| Licensing   | Existing seeds from the archive, licensed for production use      |
| Commission  | New compositions designed to your palette, repeat, and product    |
| Systems     | A private generative tool built for your studio's ongoing use     |

To begin a conversation: [reyhanehdaneshdoost@gmail.com](mailto:reyhanehdaneshdoost@gmail.com)

---

## Series

Fixed Wave is part of a computational textile series. Each volume approaches ornament from a different structural angle:

| Volume                     | Structure                    | Motion                     |
|----------------------------|------------------------------|----------------------------|
| Girih 1                    | Islamic geometric            | Static                     |
| Arachne                    | Rotating rings               | Static                     |
| Baroque Me Baby            | Baroque frames               | Static                     |
| Bezier 1                   | Concentric curves            | Static                     |
| Bezier 2                   | Single rotating curve        | Animated (plate)           |
| Brownian Graphe            | Graph networks               | Animated + interactive     |
| Celestial Grove            | Recursive branch trees       | Static                     |
| ChaotiColor                | Cellular automata            | Static                     |
| Citrus Mosaic              | Arc-and-triangle tiles       | Static                     |
| Crazy Knight Curve         | Knight's-tour smooth path    | Static                     |
| Crazy Knight Line          | Knight's-tour gradient       | Static                     |
| Crazy Letter               | Framed wavy lines            | Static                     |
| cyPollock                  | Scattered branch field       | Static                     |
| Digital Pollen             | Noise-driven texture         | Static                     |
| Draconic Fractals          | Tiled dragon curve           | Static                     |
| Dreamscape Watercolors     | Layered watercolor blooms    | Static                     |
| Elliott Waves              | Financial chart              | Static                     |
| Ellipses                   | Concentric elliptical rings  | Static                     |
| Enigma Sudoku              | Playable 9×9 puzzle          | Interactive (plate)        |
| Ephemeral Whirls           | Wandering looper field       | Static                     |
| Eyes                       | Layered iris portrait        | Static                     |
| Fibonacci Fourier          | Harmonic line field          | Animated (plate)           |
| **Fixed Wave**             | **Wave-equation field**      | **Animated (plate)**       |

The series is designed as a coherent whole — same page structure, same seed logic, same licensing and commission terms — so that each volume can be presented individually or as part of a larger body of work.

---

## Credits

- **Design & Generative System** — Reyhaneh Daneshdoost
- **Typefaces** — Cormorant Garamond · DM Mono
- **Platform** — Reyrove Studio
- **Edition** — Fixed Wave, Autumn 2026

### On AI tools

Where technical obstacles were encountered, AI tools were used for debugging and code optimization. Every structural, aesthetic, and conceptual decision remained the artist's own.

---

## Links

- Website — [reyrove.github.io](https://reyrove.github.io/)
- Instagram — [@rey._.rove](https://www.instagram.com/rey._.rove/)
- LinkedIn — [Reyhaneh Daneshdoost](https://www.linkedin.com/in/reyhaneh-daneshdoost-730481160/)
- X — [@reyrove](https://x.com/reyrove)

---

© Fixed Wave · All compositions reproducible by seed · Computational Textile Design