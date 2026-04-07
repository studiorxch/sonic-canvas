# Drawing Input + Cursor Alignment — Fix Summary

## 3 files changed, 0 files added, 0 files removed

---

## 1. `ui/drawTools.js` — Coordinate mapping fix + coalesced events

### Problem

`getCanvasCoords` used `getBoundingClientRect()` directly, but the canvas uses
`object-fit: contain`. When the viewport aspect ratio differs from 1080:1920,
the canvas content is letterboxed/pillarboxed inside its element rect.
`getBoundingClientRect()` returns the full element box — not the visible content
area — so all coordinates were offset.

### Fix

Added `getContentRect(canvas)` that computes the actual rendered content rect
by calculating the letterbox/pillarbox offsets from the aspect ratio mismatch.
`getCanvasCoords` now maps through this corrected rect.

This fixes: drawing offset, shape placement offset, ball spawn offset,
selection/hit detection offset — all in one place.

### Additional: Coalesced pointer events

Added `event.getCoalescedEvents()` in the draw-mode `pointermove` handler.
Browsers coalesce high-frequency pointer events between frames; using coalesced
events captures all intermediate points for smoother, less choppy strokes.

---

## 2. `main.js` — Two targeted line changes

### Change A: Segment length threshold (line ~420 in createSegmentsBatch)

```
-  if (length < 6) {
+  if (length < 2) {
```

The smoothing pipeline (simplify → smooth → segment) produces many short
segments. At threshold 6, short segments were dropped, creating visible gaps
(the "dashed" appearance). Lowering to 2 preserves stroke continuity.

### Change B: Text input positioning (beginCanvasTextInput)

Applied the same `object-fit: contain` content-rect calculation used in
drawTools, so the floating text input appears at the correct canvas position
instead of being offset.

---

## 3. `styles.css` — One property added

```css
canvas {
  /* existing rules unchanged */
+ touch-action: none;
}
```

Prevents the browser from intercepting touch/pen input for scrolling or
gestures, ensuring all pointer events reach the canvas immediately.

---

## What was NOT changed

- Transport system (recording, looping, BPM)
- MIDI system
- Physics / collision engine
- Canvas renderer
- Scene manager
- UI layout / controls
- Line system
- Text system
- Swarm system
- SVG importer
- Example preset
