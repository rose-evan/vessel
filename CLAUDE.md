# Vessel — Project Instructions for Claude

## Project Overview
Vessel is an interactive demo for the Unitree B2 quadruped robot. Users type natural language commands; the 3D scene responds with robot animations and spawned objects. The core thesis: **robotics is following the same scaling trajectory as LLMs — more data + compute = emergent capabilities.** Every robot action surfaces the massive training infrastructure behind it.

## Architecture
Single `index.html` — no build step, no module bundler. All code is inline using:
- Three.js via CDN `<script src>` (global `THREE`)
- React + ReactDOM via CDN UMD builds (globals `React`, `ReactDOM`)
- Babel Standalone (`<script type="text/babel">`) to parse JSX inline

Do NOT switch to ES modules / importmap — browsers reject `.jsx` extensions and MIME types become an issue without a proper dev server that sets them correctly.

## Tech Stack
- React 18 (CDN UMD, unpkg)
- Three.js r134 (CDN, cdnjs)
- Babel Standalone (CDN, unpkg) — JSX transform only, no bundling
- All CSS inline in `index.html`

## Key File
| Path | Purpose |
|------|---------|
| `index.html` | **Everything** — styles, Three.js scene, robot, animations, React UI |

## Running
Serve with any static server (needed for GLB fetch):
```bash
npx serve .          # → http://localhost:3000
python3 -m http.server 8080  # → http://localhost:8080
```

## Code Conventions
- Primary robot is a GLB model loaded via `THREE.GLTFLoader` (`robot.glb` in project root), with a procedural B2 fallback if the GLB fails to load
- GLB animations via `THREE.AnimationMixer`; procedural fallback uses `QuadrupedAnimator` with pose blending
- Animation states (quadruped): `idle`, `trot`, `sit`, `inspect`, `alert`, `patrol`
- Commands processed locally via keyword matching (`BUILT_IN_COMMANDS`) — no API key needed
- Each command has a `training` object with: `simSteps`, `gpuHours`, `datasetTB`, `modelParams`, `envCount`
- Spawn items: `box`, `sphere`, `cylinder`, `table`, `conveyor_belt`, `package_stack`

## UI Layout
- Warm white background (`#f5f3ee`), full-bleed 3D scene fills viewport
- Frosted glass top bar: "VESSEL" wordmark + cumulative compute counter (sim steps, GPU-hrs, TB data)
- Scaling Curve panel (top-right): SVG power-law chart with robot progress vs ghosted LLM reference curve, "We are here" marker
- Floating bottom stack (centered): DataPanel (animated training metrics) → suggestion chips → input bar
- DataPanel: 4 animated counters (Sim Steps, GPU Hours, Dataset TB, Parameters) + narration line
- Quick-action chips: Patrol, Inspect, Navigate, Search, Reset
- All overlays use `backdrop-filter: blur()` glass effect

## Key React Components
- `DataPanel` — animated counter grid showing training requirements per command
- `ScalingCurve` — SVG power-law curve (robotics vs LLMs) that builds with each command
- `TopBar` — wordmark + cumulative compute banner
- `useAnimatedNumber` — hook for counting-up number animations

## Preferences
- Demo day quality — designed for **live presentation** on a projector
- Data numbers are the visual hero (28px+), narration is secondary
- Cinematic camera: slow auto-orbit, dramatic low angle
- `#FF4D00` is the accent orange used throughout
- Fonts: Syne (display/wordmark), Outfit (body/UI)
