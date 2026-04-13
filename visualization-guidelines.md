# Cobre — Visualization & Documentation Design Guidelines

**Date:** 2026-03-24
**Scope:** Visual identity application for landing page, mdBook, diagrams,
interactive elements, and all public-facing documentation

---

## 1. Design Principles

These extend the brand guidelines into the interactive and documentation space.

### 1.1 Power systems visual language, not generic data viz

Every diagram should be immediately recognizable to a power systems engineer.
Reservoirs look like dams, not blue rectangles. Generators use domain-standard
symbols (circle for hydro, circle with flame for thermal, circle with propeller
for wind). Buses are thick horizontal bars. Transmission lines connect them
with power flow indicators. The visual vocabulary comes from one-line diagrams
and control room displays — the tools this audience already uses.

### 1.2 Dark-first, copper-warm

The default theme is dark (midnight `#0F1419` background). Copper accents
(`#B87333`) provide warmth without overwhelming. Diagrams use the brand
palette exclusively — no generic blues or grays from chart libraries.
Light theme is supported but secondary.

### 1.3 Data-dense when needed, animated when instructive

Power systems engineers work with tables of 165 hydros and 120 stages.
Don't hide density behind "clean" design. But when animation reveals a
mechanism (water flowing through a turbine, power dispatching across a grid,
convergence happening over iterations), use it. Animation serves
understanding, not decoration.

### 1.4 Three audiences, one identity

NEWAVE users, newcomers, and Python users all see the same visual language.
The content differs by path; the look and feel is consistent. The copper
palette, IBM Plex typography, and power-system iconography appear everywhere.

---

## 2. Color Palette (from brand guidelines)

### Primary
| Name | Hex | Usage |
|------|-----|-------|
| Copper | `#B87333` | Brand primary, accents, interactive highlights |
| Copper Light | `#D4956A` | Hover states, gradient highlights |
| Copper Dark | `#8B5E3C` | Pressed states, depth |
| Patina | `#4A8B6F` | Success, hydro/water associations, code prompts |

### Accent
| Name | Hex | Usage |
|------|-----|-------|
| Spark Amber | `#F5A623` | Thermal generation, warnings, energy indicators |
| Signal Red | `#DC4C4C` | Deficit, errors, critical states |
| Flow Blue | `#4A90B8` | Water, hydro, links, informational |

### Neutral (dark theme)
| Name | Hex | Usage |
|------|-----|-------|
| Midnight | `#0F1419` | Page background |
| Surface | `#1A2028` | Cards, elevated surfaces |
| Border | `#2D3440` | Dividers |
| Muted | `#8B9298` | Secondary text |
| Body | `#C8C6C2` | Primary body text |
| Bright | `#E8E6E3` | Headings |

### Generation color mapping
These are fixed across all visualizations — a user should always know what
color means what source:

| Source | Color | Hex |
|--------|-------|-----|
| Hydro | Flow Blue | `#4A90B8` |
| Thermal (cheap) | Spark Amber | `#F5A623` |
| Thermal (expensive) | Signal Red | `#DC4C4C` |
| NCS (wind/solar) | Patina | `#4A8B6F` |
| Deficit / unserved | Bright (dimmed) | `#E8E6E3` at 15% opacity |
| Reservoir water | Flow Blue gradient | `#4A90B8` at 15–45% opacity |
| Spillage | Copper Dark | `#8B5E3C` |

---

## 3. Typography

| Context | Font | Weight | Size |
|---------|------|--------|------|
| Headings | IBM Plex Sans | 500 | 1.5–2.5rem |
| Body text | IBM Plex Sans | 400 | 0.85–1rem |
| Labels, captions | IBM Plex Sans | 400–500 | 0.65–0.75rem |
| Code, monospace values | JetBrains Mono | 400–500 | 0.8–0.85rem |
| Metric numbers | JetBrains Mono | 500 | 1.1–1.75rem |

Load from Google Fonts:
```html
<link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Sans:wght@300;400;500;600&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet">
```

---

## 4. Tech Stack for Visualizations

### Core (always available)
| Library | CDN | Purpose |
|---------|-----|---------|
| GSAP 3.x | `cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/gsap.min.js` | Timeline animation, easing, SVG transforms |
| GSAP MotionPath | `cdnjs.cloudflare.com/ajax/libs/gsap/3.12.5/MotionPathPlugin.min.js` | Animate elements along SVG paths (water flow, power flow) |
| D3.js 7.x | `cdnjs.cloudflare.com/ajax/libs/d3/7.8.5/d3.min.js` | Custom SVG diagrams, data binding, transitions |

### Optional (use when needed)
| Library | CDN | Purpose |
|---------|-----|---------|
| anime.js 3.x | `cdnjs.cloudflare.com/ajax/libs/animejs/3.2.2/anime.min.js` | SVG path animation, staggered effects |
| Chart.js 4.x | `cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js` | Standard charts (convergence, cost breakdown) |
| Three.js r128 | Only for landing page hero if 3D is pursued | 3D power system schematic (deferred) |

### Not used
- No React, Vue, or framework dependencies on the landing page
- No Lottie or Rive (require external editors)
- No particle physics libraries (spectacle without information)
- No Canvas for diagrams (SVG is inspectable, scalable, animatable)

### Decision rules
- **Static diagram in the book** → SVG, hand-crafted or D3-generated, exported as `.svg` file
- **Interactive diagram in the book** → HTML page linked from mdBook, GSAP + D3
- **Landing page animation** → Inline SVG + GSAP timeline, self-contained in `index.html`
- **Charts (convergence, cost)** → Chart.js with brand colors hardcoded

---

## 5. Power System Visual Vocabulary

### 5.1 One-line diagram symbols

These are standard IEC/ANSI symbols adapted for the brand:

| Element | Symbol | Visual |
|---------|--------|--------|
| Bus | Thick horizontal bar | `<rect>` with rounded ends, 4px height, full width |
| Hydro generator | Circle with turbine blades | `<circle>` + 3 `<line>` radials, Flow Blue stroke |
| Thermal generator | Circle with flame | `<circle>` + flame `<path>`, Spark Amber stroke |
| NCS (wind) | Circle with propeller | `<circle>` + 3-blade `<path>`, Patina stroke |
| Transmission line | Path between buses | `<path>` with animated dash for power flow |
| Transformer | Two overlapping circles | Standard T symbol |
| Load | Downward arrow from bus | Arrow pointing down into demand label |
| Reservoir | Dam cross-section | Curved wall `<path>` + water fill `<clipPath>` |
| Spillway | Overflow path from dam | Arc over dam wall, copper dark stroke |

### 5.2 Power flow animation

Animated dashes along transmission lines indicate power flow:
- Dash speed ∝ MW flow magnitude
- Dash direction = flow direction (from exporting to importing bus)
- Line stroke-width ∝ MW flow (1px min, 3px max)
- Idle lines (zero flow): thin, no animation, border color

Implementation: CSS `stroke-dasharray` + `stroke-dashoffset` animation,
or GSAP `drawSVG` for controlled sequencing.

### 5.3 Reservoir visualization

The reservoir is a dam cross-section, not a rectangle:
- Dam wall: curved `<path>` (thicker at base, narrower at crest)
- Water body: filled area behind the dam, clipped to the dam shape
- Water level: animated via `<clipPath>` y-offset, bound to slider
- Penstock: pipe from reservoir base through dam to turbine house
- Turbine: rotating circle at penstock exit
- Tailrace: water exit below dam
- Spillway: curved path over dam crest, activated when storage > max

Water gradient: vertical, from `rgba(74,144,184,0.15)` at surface to
`rgba(74,144,184,0.45)` at depth.

### 5.4 Generation dispatch visualization

Stacked area or stacked bar, always in this order (bottom to top):
1. Hydro (Flow Blue)
2. NCS (Patina)
3. Thermal cheap (Spark Amber)
4. Thermal expensive (Signal Red, dimmer)
5. Deficit (Bright, 15% opacity)

Demand line overlaid as dashed white line.
Reservoir level as secondary axis line in Copper.

---

## 6. Page-Specific Guidelines

### 6.1 Landing page (`cobre-rs.dev`)

**Hero section:** Animated power system schematic. A one-line diagram with
4 buses (representing the Brazilian subsystems), hydro and thermal generators,
transmission lines with animated power flow, and a central reservoir that
responds to a time slider. As the user scrubs through stages, the dispatch
changes, the reservoir drains, thermals ramp up. Auto-plays slowly if the
user doesn't interact.

**Tech:** Inline SVG + GSAP timeline. Self-contained, no external data.
Must load in < 2 seconds, animate at 60fps, work on mobile.

**Below the fold:** Three audience cards, metric numbers, tabbed code
examples (CLI / Python / Convert). All static HTML+CSS.

### 6.2 Book tutorial pages

**Conceptual diagrams:** Static SVG with brand colors. The water balance
tradeoff diagram, the SDDP algorithm flow, the forward/backward pass
sequence. These are embedded as `.svg` files in the mdBook `src/images/`
directory.

**Interactive simulators:** Linked as standalone HTML pages. The hydro
dispatch simulator, the convergence explorer. mdBook embeds them via
`<iframe>` or links to a hosted version.

**Charts from real data:** Generated from 1dtoy output using matplotlib
with the brand palette, saved as PNG/SVG, embedded in the book. The
convergence plot, the dispatch timeline, the cost breakdown.

### 6.3 NEWAVE migration page

**Entity mapping diagram:** Static SVG. NEWAVE files on the left, Cobre
files on the right, copper arrows connecting them. Already designed.

**Bounds comparison table:** HTML table with conditional formatting —
green for matches, amber for near-matches, red for mismatches. Generated
by `cobre-bridge compare` output.

**Dashboard screenshot:** PNG of the Plotly dashboard from cobre-bridge,
showing a real converted case. This is the credibility image.

### 6.4 Python quickstart page

**Rendered notebook cells:** Static HTML export of the quickstart notebook
with output visible. The convergence plot and DataFrame display are the
visual payoff.

**polars DataFrame screenshot:** Shows Arrow zero-copy in action —
the simulation results loaded as a polars table.

---

## 7. Animation Guidelines

### 7.1 Timing

| Animation type | Duration | Easing |
|---------------|----------|--------|
| State transition (slider → visual) | 0.5–0.7s | `power2.out` (GSAP) or `cubic-bezier(0.4, 0, 0.2, 1)` |
| Continuous flow (water, power) | 2–4s loop | `linear` |
| Entry animation (page load) | 0.8–1.2s stagger | `power3.out` |
| Hover effect | 0.15–0.2s | `ease` |
| Number counting | 0.6s | `power1.inOut` |

### 7.2 Motion principles

- **Meaningful motion only.** Every animation must communicate something:
  water flowing = dispatch happening, turbine spinning = generation active,
  line pulsing = power flowing. No decorative wobbles or bounces.
- **Interruptible.** Slider drags immediately update the target state;
  animations lerp to the new value, they don't queue.
- **Reduced motion respected.** Wrap all `@keyframes` in
  `@media (prefers-reduced-motion: no-preference)`. Provide static
  fallbacks that show the same information without motion.
- **60fps or nothing.** Animate only `transform` and `opacity` where
  possible. SVG attribute animations (changing `d`, `cx`, `cy`) are
  acceptable for diagrams but must be throttled via `requestAnimationFrame`.

### 7.3 Interactive controls

- **Sliders** for continuous parameters (storage, inflow, demand)
- **Toggle switches** for binary states (heating on/off, lags enabled)
- **Play/pause/scrub** for timeline animations (stage progression)
- **Hover tooltips** for data values on diagram elements
- **Click to drill down** on buses/generators for detail panels

Controls use the brand styling: copper thumb on range sliders, patina
for active toggles, surface background for control panels.

---

## 8. File Organization

```
cobre-rs.dev repo (or cobre-rs/.github):
  index.html              ← Landing page (single file, inline CSS/JS/SVG)
  assets/
    cobre-logo-dark.svg
    cobre-logo-light.svg
    cobre-icon.svg
    og-image.png          ← Social sharing preview (1200x630)

cobre/book/:
  src/
    images/
      water-balance.svg           ← Conceptual diagram (static)
      newave-mapping.svg          ← Entity mapping diagram (static)
      one-line-diagram.svg        ← System schematic (static)
      convergence-annotated.png   ← Generated from 1dtoy output
      dispatch-timeline.png       ← Generated from 1dtoy output
    interactive/
      hydro-dispatch.html         ← Interactive simulator
      convergence-explorer.html   ← Interactive convergence plot
  theme/
    css/
      custom.css                  ← Brand palette applied to mdBook
```

---

## 9. Quality Checklist

Before shipping any visualization:

- [ ] Uses only brand palette colors (no Chart.js defaults, no generic blues)
- [ ] IBM Plex Sans for text, JetBrains Mono for numbers/code
- [ ] Works in dark mode (midnight background) — test explicitly
- [ ] Works in light mode (secondary priority, but must not break)
- [ ] Reduced motion fallback exists (`@media (prefers-reduced-motion)`)
- [ ] Loads in < 2 seconds on a 3G connection (no heavy libraries for static pages)
- [ ] Mobile responsive (scales down to 375px width without horizontal scroll)
- [ ] Power system visual vocabulary used (not generic chart/diagram shapes)
- [ ] Generation colors follow the fixed mapping (hydro=blue, thermal=amber, etc.)
- [ ] Interactive controls use brand styling (copper slider thumbs, etc.)