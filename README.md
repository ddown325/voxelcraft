# VoxelCraft

A Minecraft-style voxel sandbox that lives in **one HTML file**. Open `index.html` (or the GitHub Pages link below) and you're standing on an island with **flowing water**, a day/night cycle, a **survival mode** with hearts and drowning, **TNT**, **falling sand**, ores, 30 new blocks, timed block mining, a chunky lit first-person arm, a full inventory, and a world that saves itself to your browser.

**▶ Play it:** <https://ddown325.github.io/voxelcraft/>

![Survival](screenshots/survival.jpg)

## What's new in v4 — the current update

### Survival mode, for real
- **Hearts and air**: 10 hearts (20 HP) and a 10-bubble air meter above the hotbar. **Fall damage**, **drowning** when your air runs out, **cactus pricks**, **TNT blasts** and **the void** all hurt. You **regenerate** slowly when out of danger for 6 seconds.
- **Death screen** — "YOU DIED" with the cause, a Respawn button (back at spawn, full health) and Title Screen. Damage flashes a red vignette and jolts the hearts.
- Survival is **earned**: the creative palette is hidden, flight is disabled, mining takes time and every placed block is consumed. A small starter kit (planks, glass, water, sponge, TNT) gets you building. Switch to creative any time with **V**.

### 30 new blocks & three new block systems
- **Ores generate underground**: coal, iron, gold and diamond in 2×2×2 veins (diamonds deepest, y ≤ 12), plus a proper rugged **bedrock** floor that's unbreakable.
- **Falling sand & gravel**: unsupported sand becomes a falling entity, splashes down through water, and settles on the ground — mine carefully underneath.
- **TNT**: mine it in survival to light the fuse — it flashes, swells, then **explodes** a ~3.6-block crater with a flash + smoke column, a boom, camera shake and knockback. **Chain reactions** detonate neighbours; obsidian and bedrock resist; water absorbs the blast.
- New blocks: coal/iron/gold/diamond ore, bedrock, stone bricks, mossy cobblestone, sandstone, obsidian, ice (**slippery!**), clay, glowstone, **birch** & **spruce** logs/leaves/planks (spruce forests claim the high ground), 7 wool colours, cactus (**pricks!**), dead bush, pumpkin, bookshelf and TNT — all with procedural pixel-art textures.

### Water: gaps and mixing fixed for good
- **No more texture gaps in the water.** Wave amplitude is now driven by a **corner-averaged level** — two neighbouring cells compute *identical* values at their shared corners, so the displaced surface is continuous across every cell and chunk border. Verified seam-free.
- **No more water mixing/tearing.** Vertex wave phases are now a pure function of world position and time — the old per-cell flow advection desynced phases at shared corners and slowly tore the surface open. The visible current (advected ripples, directional foam streaks, churn on falls) lives on in the fragment stage, where it can't open gaps.

### A properly chunky arm
The arm is now genuinely **Steve-thick** — the forearm and fist boxes roughly doubled in cross-section, with a wider knuckle face, in the classic bottom-right pose with swing, sway, bob and place-recoil. No more skinny stick.

### Better inventory icons
The true-3D icon baker now renders at 144 px with a roomier classic angle and a **soft contact shadow** composited under each block — icons read as solid objects sitting in the slot, not flat stickers.

### Movement: jump like a miner
- **No auto step-up**: walking into a full block now stops you — **you must jump** (that's the point of stairs!). Swimming still lets you climb out onto the bank with a surface kick.
- **Sneak** is a careful slow walk; **ice** has almost no grip, so momentum carries you.

### Bigger render distance
The slider now runs **4–28 chunks** (default 10) with the same budgeted worker streaming.

## What was new in v3 — water that stops

### Water that actually stops (and knows why)
Three "flow forever" bugs are dead, each verified by a regression test:

- **No more runaway floods**: a splash landing on a lake used to regenerate full-height water over *every* surface cell, crawling across the entire ocean forever. Surface flow now **decays one level per block over water** like Minecraft — a waterfall pouring into the sea leaves a bounded ripple ring and settles.
- **No more floating sources**: flowing water flanked by two sources used to crystallise into a permanent source even while hovering over a void, seeding endless mid-air waterfalls. Source creation now requires solid ground (or a source) below — the real Minecraft rule.
- **No more re-flow on chunk reload**: streaming a chunk back in used to re-schedule every historical edit, making water visibly churn forever as you walked around. Only cells that are water or touch water re-settle now — plus a queue safety valve caps even a pathological flood.

### Water that reads as flowing
- Every water vertex now carries a **flow vector** (direction + speed) derived from the level gradient at mesh time. The shader **advects its ripples downstream** — waves visibly travel with the current instead of sliding across it.
- Rivers and spill-sheets show **directional current streaks** — elongated foam lines aligned with the flow, sliding at the water's own pace.
- Waterfalls render as **dense, near-opaque churning water**: fine vertical streaks up close, big slow white tongues that stay readable from far away, churn flags on every cell in a column, and a hard outward **splash-ring current** where the fall lands.
- Fixed inverted face winding on two of the four water side planes — waterfalls used to render as flat glass slabs through the back-face path.

### A proper chunky arm *(v3)*
The skinny stick is gone. The view model is now a **blocky forearm + wider fist** with knuckles, finger creases and a thumb wrap painted into the atlas, shaded per-face like terrain, in the classic bottom-right Minecraft pose. Bare fist and held-block poses are both tuned, and the dynamic lighting (day/night, sun warmth, underwater tint, mining flash) carries over.

### True 3D inventory icons *(v3)*
Every item icon is now an **actual 3D render**: an offscreen WebGL pass snapshots each block with the game's own atlas and face shading — identical to how the held block looks — instead of a skewed 2D sketch. (Falls back to the old 2D icons if a second GL context can't be created.)

### Huge render distance *(v3)*
- Worker pool scales with your CPU (up to 6), mesh uploads run on a **per-frame time budget**, the streaming scan only runs when you cross a chunk boundary, and far chunks **free their GPU meshes** while keeping voxel data — heap stays flat even at big distances.

## What was new in v2 — the water update

### Flowing water, for real
Water is a **fluid simulation**, not a static plane:

- **8 flow levels** stored per voxel (source + 7 decaying levels). Water spreads one level weaker per block, so a stream thins as it travels and stops after 7 blocks — exactly like Minecraft.
- **Waterfalls**: water prefers falling. A cell with air below pours down one cell per tick, building cascades you can watch grow — with mist and bubbles kicking up where it lands.
- **Cliff rule**: a stream that reaches an edge pours off it as a full falling column (mountain springs work).
- **Infinite pools**: flank a gap with two sources (on solid ground) and it crystallises into a new source — build classic infinite wells.
- **Draining**: remove the source (or soak it up) and the flood drains away level-by-level, waterfalls collapsing top-down.
- **Sponge**: place it to soak up every water cell in a 5×5×5 cube — the way to un-flood a build.
- **Placeable water**: water and sponge live in the block palette (E), so flowing-water play needs no grind.
- Thin flowing water renders flatter waves and streaky rapids foam; falling columns render full-height with a deep-blue body. Swimming, diving, raycasting and spawning all understand every flow level.

### First-person arm with real lighting
A proper Steve-style arm (procedural skin + sleeve texture) holds the view:

- Sleeve enters from the bottom-right, fist reaches toward centre — the classic pose, with the held block riding at the fist.
- **The arm is lit by the world**: bright at noon, dim at midnight, warm in direct sun, blue-tinted underwater, and it flashes on mining impact. The tint is driven by the same lighting rig as the terrain.
- Look **sway** (the arm lags a touch behind camera rotation), walk bob, swing arcs, and place-recoil.

## Features

### Water, first-class
Every water surface is a single custom shader — no cubemaps, no render targets, no post-processing:

- **Three directional wave trains** (sum of sines) displace the surface and yield an analytic normal per-vertex, plus a wave-height varying for subsurface glow. Flowing water scales its waves down to a thin film, and the wave phases **ride the flow vector** so ripples travel downstream.
- **Schlick fresnel** blends the body colour into an analytic sky reflection computed along the reflected ray.
- **Depth-graduated body colour** — turquoise shallows fading to deep blue — driven by a per-vertex water-column depth attribute baked at mesh time.
- **Blinn-Phong sun glitter** that widens at low sun angles, micro-glints riding a scrolling ripple-noise field, and animated **shore foam** banded by noise near shallow columns.
- **Flow-directional current streaks** on moving water, **churn + white tongues** on falls, and splash-ring churn where falls land.
- **The underside is a first-class citizen**: looking up from below you see a brighter, transmissive surface with the sun still glittering through the waves.
- **Underwater atmosphere** — dense teal fog, animated caustics projected onto submerged geometry (terrain shader), rising bubble particles, a low-pass audio filter, and a screen-space tint.
- Sound design to match: waves lap louder near shore, swimming gets its own loop, splashes burst on entry.

### A real break system
- Hold left-click to mine: blocks have **per-type hardness**, a **10-stage crack overlay** grows on the targeted block, debris puffs kick up while you work, and the block pops with a burst of particles and a synthesized dig sound.
- Broken blocks **drop into your inventory** (stone drops cobblestone, like the original).

### Inventory
- 9-slot hotbar (keys 1–9 / scroll wheel) + 27-slot storage opened with **E**.
- Pick up / place / swap / merge stacks, right-click to split, shift-click to quick-move.
- **Creative mode** (V): instant break, infinite stacks, flight (F / double-space), and the palette with every block — plus water and sponge. Survival hides the palette: mine what you place.

### Saves
The whole world persists to `localStorage` — blocks you placed or broke (including every flow level) are stored as a **sparse diff against the generated terrain** and re-applied the instant a chunk regenerates, at which point the fluid sim re-settles around them. Player position, look, inventory, time of day and mode all round-trip. Autosave every 20 s, on pause, and on tab close.

### Performance
- Terrain generation, face culling, vertex AO and water meshing (including the flow field) all run in **Web Workers** (Blob-based, zero bundler config), transferring buffers zero-copy.
- Worker pool scales with the CPU (3–6 workers); chunks stream in a distance-sorted queue and mesh uploads run on a **per-frame time budget**; the streaming scan only fires when you cross a chunk boundary.
- Far chunks **free their GPU meshes** while keeping voxel data until they fully unload — flat memory at huge render distances.
- The fluid sim processes at most 96 cells per 0.2 s tick, and only cells whose neighbourhood changed — a settled ocean costs zero work per frame, with a hard queue cap as a runaway-flood safety valve.
- One draw call per chunk per pass (opaque / foliage / water). Distance fog hides the streaming edge.
- Seeded, deterministic worldgen and gameplay randomness (no `Math.random` in gameplay paths).

### The rest
Day/night cycle with sunrise palettes, stars and a moon; drifting blocky clouds; vertex-AO terrain with wrapped sun lighting; swaying grass and flowers; procedural 16×16 texture atlas (arm, fist, sponge and water tiles included), true-3D inventory icons, crack decals and cloud texture — all painted in code; fully synthesized Web Audio (wind, waves, footsteps, digs, UI); pointer-lock first-person controller with sprint, sneak, swim and flight.

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
| `Space` | Jump — you must jump up full blocks · swim up · double-tap to fly (creative) |
| `Shift` | Sneak / descend |
| `Ctrl` | Sprint |
| `V` | Toggle creative mode |
| `F` | Toggle flight (creative only) |
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
| ![Survival](screenshots/survival.jpg) | ![Rapids](screenshots/rapids.jpg) |
| ![Blocks](screenshots/blocks.jpg) | ![TNT](screenshots/tnt.jpg) |
| ![Water level](screenshots/water-level.jpg) | ![Waterfall](screenshots/waterfall.jpg) |
| ![Shoreline](screenshots/water.jpg) | ![Underwater](screenshots/underwater.jpg) |
| ![Mining](screenshots/mining.jpg) | ![Inventory](screenshots/inventory.jpg) |

## License

MIT — see [LICENSE](LICENSE).
