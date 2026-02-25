# Vessel — Claude Memory

## Project at a Glance
Interactive demo for Unitree B2 quadruped (dog) robot. Users type commands; 3D scene responds with robot animations and spawned objects. Core thesis: **robotics is following the same scaling trajectory as LLMs — more data + compute = emergent capabilities.** Visualized via a Scaling Curve and animated training data metrics.

## File Structure
- **`index.html`** — the ONE real file. Everything inline. This is what gets served.
- `robot.glb` — Unitree B2 GLB model (loaded first; procedural quadruped fallback if load fails)

## Architecture Constraints
- NO build step, NO bundler, NO ES modules (MIME type issues without a dev server)
- Three.js r134 via CDN globals (`THREE`)
- React 18 UMD + Babel Standalone for inline JSX (`<script type="text/babel">`)
- Serve with `npx serve .` or `python3 -m http.server 8080`

## Current `callClaude()` State (index.html)
Local mock — keyword matching via `BUILT_IN_COMMANDS` with 400–700ms delay. No API key needed.

## Three.js Scene (index.html)
- Light warm background (`#f5f3ee`) — clean studio aesthetic
- NO bloom/postprocessing — direct renderer.render()
- Clean floor with shadows, no grid
- Orange ring pulse expands from robot on each action
- Cinematic orbit: slow auto-rotate (0.0008/frame), low angle, radius 3.2
- GLB loaded first; procedural B2 quadruped (`buildB2()`) as fallback
- Spawn/despawn with scale tween + burst ring

## Robot (B2 Quadruped)
- `QuadrupedAnimator` class with states: `idle`, `trot`, `sit`, `inspect`, `alert`, `patrol`
- Poses define: bodyY, bodyRoll, bodyPitch, headYaw, headPitch, + hip/knee angles for 4 legs (FL, FR, BL, BR)
- Procedural B2: dark metallic body, 4 legs with upper/lower segments, sensor head with cyan emissive, orange accents
- GLB target height: 0.65 (quadruped scale)

## Training Data System
Each command in `BUILT_IN_COMMANDS` has a `training` object:
- `simSteps` — physics simulation steps (e.g. 2,500,000,000)
- `gpuHours` — GPU compute hours (e.g. 4,200)
- `datasetTB` — dataset size in TB (e.g. 15)
- `modelParams` — model parameter count in millions (e.g. 800)
- `envCount` — number of training environments (e.g. 100,000)

## Scaling Curve
- SVG-based React component in top-right glass panel
- Power-law curve: robotics progress (orange solid) vs LLMs (gray dashed reference)
- Each command adds a data point; curve grows progressively
- "We are here" marker on current robot position
- Footer: legend + task count

## Cumulative Compute (Top Bar)
- Running totals: sim steps, GPU-hours, TB data
- Updates with each command — numbers grow to show scale

## React UI
- `TopBar` — "VESSEL" + cumulative compute stats + status dot
- `ScalingCurve` — scaling visualization (top-right)
- `DataPanel` — animated training metrics (4-column grid: Sim Steps, GPU Hours, Dataset, Parameters)
- `bottom-stack` — DataPanel → chips → input bar
- Chips: Patrol, Inspect, Navigate, Search, Reset

## Design Tokens
- Accent: `#FF4D00`
- Background: `#f5f3ee`
- Fonts: Syne (display/wordmark), Outfit (body)
- Glass surfaces: `backdrop-filter: blur()` + `rgba(255,255,255,0.78)` background
- Data metric values: 28px+ for projector readability

## User Preferences
- Demo-day quality: designed for **live presentation** to mixed UCLA audience
- Data/scaling story is the visual hero, robot is supporting evidence
- Keep everything in `index.html` — do not split into modules
