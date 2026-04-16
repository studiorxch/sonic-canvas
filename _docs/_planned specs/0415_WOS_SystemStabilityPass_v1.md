0415_WOS_SystemStabilityPass_v1.0.0

GOAL:
Stabilize core interaction loop:
motion → collision → sound

DO NOT ADD NEW FEATURES.

PRIORITY ORDER:

1. Fix physics integrity

- restore collision detection for lines + shapes
- restore bumper behavior (hard + elastic)
- ensure particles do not pass through geometry

2. Fix emitter regression

- emitter must exist as a behavior, not a separate system
- restore all previous behaviors (attract, repel, etc.)
- restore collision highlight on contact
- ensure emitter does not override physics system

3. Add global audio normalization

- implement master gain node
- reduce output level to match standard playback (-12db target)
- prevent clipping during rapid collisions

4. Ball lifecycle control

1. Add clearBalls() (instant removal of all balls)
1. Add MAX_BALLS cap (prevent overflow)
1. Add optional spawn throttling (soft cap)
1. Add ball decay system (lifetime-based removal)

1. Particle system (minimal)

- add size control
- respect selected shape (circle/triangle/square)
- preserve color mapping

6. Silent color

- add “silent” or “mute” color
- skip audio trigger when applied

CONSTRAINTS:

- DO NOT modify saving system
- DO NOT modify sampler
- DO NOT add new UI panels
- DO NOT change layout

SUCCESS CRITERIA:

- collisions are reliable
- emitter does not break system
- audio is balanced
- system is stable for recording via OBS
