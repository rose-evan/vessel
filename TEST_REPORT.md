# Vessel Demo - Test Report

## Automated Checks ✓

### Server Status
- ✓ Server running at http://localhost:8080
- ✓ Page loads successfully (HTTP 200)
- ✓ Content size: 61,610 bytes

### Key Components Verified
- ✓ Three.js CDN loaded
- ✓ React CDN loaded  
- ✓ Babel Standalone loaded
- ✓ GLTFLoader present
- ✓ Patrol command implemented
- ✓ DataPanel component present
- ✓ ScalingCurve component present
- ✓ Console logging for GLB load
- ✓ robot.glb file exists (15MB)

## Code Analysis

### Animation System
The patrol animation is properly implemented:
- **Patrol pose function** (`patrolPose`) creates a walking gait with:
  - Frequency: 2.2 Hz
  - Leg swing: 0.22 radians
  - Diagonal leg pairing (FL+BR, FR+BL)
  - Head scanning motion (yaw: ±0.4 rad, pitch: 0.04-0.08 rad)
  - Body bob: 0.008 units
  
### Command Flow
1. User clicks "Patrol" chip
2. `handleSend('patrol the area')` called
3. `callClaude()` → `processCommand()` matches "patrol" keyword
4. Returns action: 'patrol' with training data
5. `sceneRef.current.setAction('patrol')` triggers animation
6. `robot.animator.setState('patrol')` updates animator state
7. Animation loop calls `update(dt)` → `QUAD_POSES.patrol(t)` → leg movements

### Training Data for Patrol
```javascript
{
  simSteps: 2_800_000_000,
  gpuHours: 7200,
  datasetTB: 42,
  modelParams: 2800,
  envCount: 120_000
}
```

## Expected Behavior

### Initial Load (0-10 seconds)
1. Warm white background (#f5f3ee) appears
2. Top bar with "VESSEL" wordmark fades in
3. Scaling curve panel (top-right) appears
4. 3D scene initializes with grid floor
5. Robot GLB loads (15MB, may take 5-10 seconds)
6. Console message: "Loaded B2 GLB" with size and joint count
7. Robot appears in idle pose (subtle breathing)
8. Bottom UI stack: suggestion chips + input bar

### After Clicking "Patrol" (10-12 seconds)
1. DataPanel animates in above chips
2. Four counters count up:
   - Sim Steps: 2.8B
   - GPU Hours: 7.2K
   - Dataset TB: 42
   - Parameters: 2.8B
3. Narration: "Autonomous patrol — continuous SLAM + obstacle avoidance..."
4. Robot transitions from idle → patrol (0.5s blend)
5. Legs begin walking motion (diagonal gait)
6. Head scans left/right
7. Scaling curve adds new point
8. Top bar cumulative stats update

## Manual Testing Checklist

### Visual Checks
- [ ] Background is warm white (#f5f3ee)
- [ ] Top bar has frosted glass effect
- [ ] Scaling curve shows power-law curve
- [ ] Robot model is visible and properly scaled
- [ ] Grid floor is visible
- [ ] All UI text is readable (Outfit font for body, Syne for wordmark)

### Interaction Checks
- [ ] Can click Patrol chip
- [ ] DataPanel animates in smoothly
- [ ] Numbers count up (not instant)
- [ ] Robot legs move in walking pattern
- [ ] Head rotates/scans
- [ ] No console errors
- [ ] Camera auto-orbits slowly
- [ ] Can drag to rotate camera manually

### Console Checks
Expected messages:
```
Loaded B2 GLB { size: {...}, joints: X }
```

Should NOT see:
- "GLB failed to load; using procedural B2" (means GLB didn't load)
- Any red error messages
- CORS errors
- 404 errors for robot.glb

## Known Limitations
- MCP browser tools not available in this session (cannot automate browser testing)
- Manual browser testing required for full verification
- Screenshot capture requires browser DevTools or external tool

## Recommendations
1. Open http://localhost:8080 in Chrome/Firefox
2. Open DevTools Console (Cmd+Option+J / F12)
3. Wait for "Loaded B2 GLB" message (~5-10 seconds)
4. Verify robot is visible and in idle pose
5. Click "Patrol" button
6. Verify:
   - DataPanel appears with animated counters
   - Robot legs move in walking motion
   - Head scans left/right
   - No console errors
7. Try other commands: Inspect, Navigate, Search, Reset
8. Test input bar with custom text

## Files Checked
- ✓ `/Users/evan/Desktop/Projects/Vessel/index.html` (1,735 lines)
- ✓ `/Users/evan/Desktop/Projects/Vessel/robot.glb` (15MB)
- ✓ `/Users/evan/Desktop/Projects/Vessel/CLAUDE.md` (project instructions)

---
**Status**: Code analysis complete. Manual browser testing required for full verification.
