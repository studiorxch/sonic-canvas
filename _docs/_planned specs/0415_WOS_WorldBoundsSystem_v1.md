0415_WOS_WorldBoundsSystem_v1.0.0

GOAL:
Add configurable boundary behavior per canvas edge.

FEATURE:

Add world.bounds:
{
top: "kill" | "bounce" | "wrap" | "none",
bottom: "",
left: "",
right: ""
}

IMPLEMENT:

1. applyBounds(ball, world.bounds) in physics loop
2. handleEdge() function for behavior switching
3. remove balls flagged as \_dead after update

UI:

Add "Bounds" section in World tab:

- Top
- Bottom
- Left
- Right

OPTIONS:

- Bounce
- Kill
- Wrap
- None

CONSTRAINTS:

- Do not modify emitter logic
- Do not modify collision system
- Do not modify rendering
- Keep behavior independent from physics mode

SUCCESS:

- balls correctly bounce, wrap, or get removed per edge
- supports zero-g without overflow
