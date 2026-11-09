# AGENTS.md — Country Mile

## What this is
An endless, relaxing pseudo-3D driving game. **The whole game lives in `index.html`** (inline CSS + one IIFE script). The user explicitly required a single HTML file with no external libraries, so do not add frameworks, bundlers, npm dependencies, image files or web fonts.

## Files
- `index.html`: the game: markup for the HUD and overlays, styles, and all game code.
- `netlify.toml`: static publish of the repo root, with no build command.
- `README.md`: player-facing overview.

## Script layout (in order inside `index.html`)
1. **Utilities**: colour math (`mix3`, `mul3`, `rgb`), easing, seeded `rng` (mulberry32), and a `store` wrapper around `localStorage` (keys are prefixed `countrymile.`).
2. **World constants**: `SEG`, `ROAD_W` (the *half* road width), `CAM_H`, `CAM_DEPTH`, `MAX_SPEED`, and the km/h and metre conversions.
3. **Canvas sizing**: DPR is capped at 2 and total pixels at about 2.6M. `XS` and `YS` are the horizontal and vertical projection scales. `XS` is boosted on portrait screens so the road reads well on phones.
4. **Palette**: `KEYS` are day-cycle keyframes (sky, fog, light multiplier, sun, cloud, stars). `samplePalette(t)` writes into `P`. `rebuildPalette()` runs only when the quantized time key changes. It rebuilds the per-fog-level colour string caches (`COL`) and re-tints every sprite canvas, using multiply for light and source-atop for fog at 4 fog levels.
5. **Sprite art**: procedural canvas drawings (`pineArt`, `roundTreeArt`, `barnArt`, and so on) registered in `SPRITES` with `ww`, the world width in units.
6. **Road generation**: a ring buffer of `RING` segment objects that are reused and never reallocated. `ensure(idx)` generates ahead. Sections ease curve in/hold/out and ease elevation toward a bounded random target. `decorate()` places scenery by zone (`ZONES`) and spaces obstacles further apart early on, closer later, with a minimum gap.
7. **State `S`**: `mode` is `menu` (an autopilot attract drive behind the start screen), `drive` or `rest` (the pulled-over summary screen, which is the gentle "game over").
8. **Input**: keyboard state map plus pointer drag and swipe. `S.touchTarget` is a playerX the car glides toward.
9. **AudioSys**: Web Audio graph created on first user gesture. It holds the engine oscillators, a looping noise buffer, a generative pad and pluck scheduler (lookahead `setInterval`), and one-shot effects.
10. **update(dt)**: steering, centrifugal drift, speed easing toward a cruise speed that grows with distance, boost and cooldown, obstacle hits (speed ×0.72, shake, never fatal), milestones and toasts.
11. **render(dt)**: sky (gradient, stars, sun/moon, clouds, birds), then parallax layers, then road segments near-to-far with `maxy` clipping, then sprites far-to-near clipped by `seg.clip`, then fireflies, then car, then vignette.

## Conventions and non-obvious decisions
- Segments store absolute world z (`index * SEG`). There's no track looping, so the ring index is `index % RING`. Always check `seg.index === i` before trusting a ring slot that may not be generated yet.
- Elevation is bounded (±45 segments × `SEG`) so floats stay small. Position grows without bound, which is fine for float64.
- Never allocate per-frame objects in hot loops. Colours come from caches, and segment and point objects are reused.
- Sprites are anchored bottom-centre. `off` is measured in half-road units (±1 is the road edge). Keep scenery |off| ≥ about 1.3 plus half the sprite width so it never sits on the asphalt.
- Obstacles must stay "soft". They slow the car but never end the run. Keep the calm tone in all copy and toasts.
- The HUD and overlays are DOM elements, not canvas, for crisp text and accessibility. The HUD text updates at 10 Hz.
