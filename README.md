# VoxelCraft

A Minecraft-style voxel sandbox that lives in **one HTML file**. Open `index.html` (or the GitHub Pages link below) and you're standing on an island with **flowing water**, a day/night cycle, timed block mining, a lit first-person arm, a full inventory, and a world that saves itself to your browser.

**▶ Play it:** <https://ddown325.github.io/voxelcraft/>

![Waterfall](screenshots/flow.jpg)

## What's new in v2 — the water update

### Flowing water, for real
Water is now a **fluid simulation**, not a static plane:

- **8 flow levels** stored per voxel (source + 7 decaying levels). Water spreads one level weaker per block, so a stream thins as it travels and stops after 7 blocks — exactly like Minecraft.
- **Waterfalls**: water prefers falling. A cell with air below pours down one cell per tick, building cascades you can watch grow — with mist and bubbles kicking up where it lands.
- **Cliff rule**: a stream that reaches an edge pours off it as a full falling column (mountain springs work).
- **Infinite pools**: flank a gap with two sources and it crystallises into a new source — build classic infinite wells.
- **Draining**: remove the source (or soak it up) and the flood drains away level-by-level, waterfalls collapsing top-down.
- **Sponge** (new block): place it to soak up every water cell in a 5×5×5 cube — the way to un-flood a build.
- **Placeable water**: water and sponge live in the block palette (E), so flowing-water play needs no grind.
- Thin flowing water renders **flatter waves** and streaky white **rapids foam**; falling columns render full-height with a deep-blue body. Swimming, diving, raycasting and spawning all understand every flow level.

### First-person arm with real lighting
A proper Steve-style arm (procedural skin + sleeve texture) replaces the floating block:

- Sleeve enters from the bottom-right, fist reaches toward centre — the classic pose, with the held block riding at the fist.
- **The arm is lit by the world**: bright at noon, dim at midnight, warm in direct sun, blue-tinted underwater, and it flashes on mining impact. The tint is driven by the same lighting rig as the terrain.
- Look **sway** (the arm lags a touch behind camera rotation), walk bob, swing arcs, and place-recoil.

### See farther
Render distance now scales **3–10 chunks** (was 3–8): quality presets are 4/6/8, and the slider goes to 10 with fog that adapts.

## Features

### Water, first-class
Every water surface is a single custom shader — no cubemaps, no render targets, no post-processing:

- **Three directional wave trains** (sum of sines) displace the surface and yield an analytic normal per-vertex, plus a wave-height varying for subsurface glow. Flowing water scales its waves down to a thin film.
- **Schlick fresnel** blends the body colour into an analytic sky reflection computed along the reflected ray.
- **Depth-graduated body colour** — turquoise shallows fading to deep blue — driven by a per-vertex water-column depth attribute baked at mesh time.
- **Blinn-Phong sun glitter** that widens at low sun angles, micro-glints riding a scrolling ripple-noise field, and animated **shore foam** banded by noise near shallow columns — plus streaky rapids on flowing cells.
- **The underside is a first-class citizen**: looking up from below you see a brighter, transmissive surface with the sun still glittering through the waves.
- **Underwater atmosphere** — dense teal fog, animated caustics projected onto submerged geometry (terrain shader), rising bubble particles, a low-pass audio filter, and a screen-space tint.
- Sound design to match: waves lap louder near shore, swimming gets its own loop, splashes burst on entry.

### A real break system
- Hold left-click to mine: blocks have **per-type hardness**, a **10-stage crack overlay** grows on the targeted block, debris puffs kick up while you work, and the block pops with a burst of particles and a synthesized dig sound.
- Broken blocks **drop into your inventory** (stone drops cobblestone, like the original).

### Inventory
- 9-slot hotbar (keys 1–9 / scroll wheel) + 27-slot storage opened with **E**.
- Pick up / place / swap / merge stacks, right-click to split, shift-click to quick-move.
- **Creative mode** (V): instant break, infinite stacks. The palette includes every block — plus water and sponge.

### Saves
The whole world persists to `localStorage` — blocks you placed or broke (including every flow level) are stored as a **sparse diff against the generated terrain** and re-applied the instant a chunk regenerates, at which point the fluid sim re-settles around them. Player position, look, inventory, time of day and mode all round-trip. Autosave every 20 s, on pause, and on tab close.

### Performance
- Terrain generation, face culling, vertex AO and water meshing all run in **Web Workers** (Blob-based, zero bundler config), transferring buffers zero-copy.
- Chunks stream in a distance-sorted queue; mesh uploads are frame-budgeted; far chunks unload and dispose.
- The fluid sim processes at most 64 cells per 0.2 s tick, and only cells whose neighbourhood changed — a settled ocean costs zero work per frame.
- One draw call per chunk per pass (opaque / foliage / water). Distance fog hides the streaming edge.
- Seeded, deterministic worldgen and gameplay randomness (no `Math.random` in gameplay paths).

### The rest
Day/night cycle with sunrise palettes, stars and a moon; drifting blocky clouds; vertex-AO terrain with wrapped sun lighting; swaying grass and flowers; procedural 16×16 texture atlas (arm, sponge and water tiles included), isometric inventory icons, crack decals and cloud texture — all painted in code; fully synthesized Web Audio (wind, waves, footsteps, digs, UI); pointer-lock first-person controller with sprint, sneak, swim and flight.

## Controls

| Key | Action |
| --- | --- |
| `W A S D` | Move |
| Mouse | Look |
| Left click (hold) | Mine — cracks show progress |
| Right click | Place selected block |
| Middle click | Pick block |
| `1`–`9` / wheel | Hotbar |
| `E` | Inventory |
| `Space` | Jump / swim up · double-tap to fly |
| `Shift` | Sneak / descend |
| `Ctrl` | Sprint |
| `V` | Toggle creative mode |
| `F3` | Debug overlay |
| `Esc` | Pause |

## Tech

- [Three.js](https://threejs.org/) r160 (only dependency, via CDN + import map)
- Vanilla ES modules, zero build step
- Web Workers for chunk generation & meshing
- Custom GLSL for water, sky and terrain lighting
- 0.2 s fixed-tick fluid simulation on the main thread
- `localStorage` persistence

## Run it

No build, no install — open `index.html` in any modern desktop browser, or serve it statically (this is exactly what GitHub Pages does). Works from `file://` too.

## Screenshots

| | |
| --- | --- |
| ![Flowing water](screenshots/flow.jpg) | ![Waterfall](screenshots/waterfall.jpg) |
| ![Shoreline](screenshots/water.jpg) | ![Underwater](screenshots/underwater.jpg) |
| ![Mining](screenshots/mining.jpg) | ![Inventory](screenshots/inventory.jpg) |

## License

MIT — see [LICENSE](LICENSE).
