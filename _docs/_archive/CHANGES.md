# Shape System — Build Summary

## What was built

Shape container + segment model + selection system, running parallel to `state.lines`.

## Files

### NEW: `engine/shapeSystem.js`
Shape/Segment data model. Must be loaded in index.html after `textSystem.js`, before `collision.js`.

**Data model:**
- `Shape` = { id, kind, position, rotation, scale, segments[], isExpanded, bounds }
- `Segment` = { id, type, x1, y1, x2, y2, color, thickness, note, midiChannel, behavior, life, lastHitAt }

**Functions:**
- `createShapeFromPoints(kind, pointArrays, position, settings)` — factory from SHAPE_LIBRARY paths
- `findShapeAtPoint(shapes, point, threshold)` — shape-level hit test
- `findSegmentAtPoint(shape, point, threshold)` — segment-level hit test (edit mode)
- `translateShape(shape, dx, dy)` — moves all segments + position
- `duplicateShape(shape)` — deep copy with new IDs
- `serializeShape / hydrateShape` — save/load
- `getCollisionSegments(shapes)` — exposes segments as collision-compatible objects for physics
- `markSegmentHit(seg, now)` — collision highlight back-reference

### CHANGED: `render/canvasRenderer.js`
- Removed debug `console.log` from `drawLines`
- Added `drawShapes()` function — renders `state.shapes` with same visual language as lines
- Shape selection: white dashed bounding box (normal), gold dashed (edit mode)
- Segment selection: gold highlight overlay when shape is expanded
- Inserted into render pipeline between `drawLines` and `drawBalls`

### CHANGED: `ui/drawTools.js`
- Added shape hit testing to select tool (between text and line checks)
- Added double-click detection (400ms window) for edit mode toggle
- In edit mode: click tries segment selection first, falls back to shape selection
- Added callbacks: `onSelectShape`, `onSelectSegment`, `onDoubleClickShape`
- Removed debug `console.log` from pointerup
- Removed `requestAnimationFrame` delay on pointerup reset

### CHANGED: `main.js`
- Added `state.shapes`, `state.selectedShapeId`, `state.selectedSegmentId`
- Fixed `createSegmentsBatch` (was creating raw objects, now uses `normalizeLineObject(SBE.LineSystem.createLine())`)
- Redirected `createShapeAt` to create Shape objects in `state.shapes` via ShapeSystem
- Added `selectShape()`, `getSelectedShape()`, `toggleShapeEditMode()`
- Updated `selectObject` / `clearSelection` to clear shape selection
- Updated `translateSelection` — shapes move via `ShapeSystem.translateShape`
- Updated `deleteSelectionObject` — removes from `state.shapes`
- Updated `duplicateSelectedObject` — uses `ShapeSystem.duplicateShape`
- Updated `pushHistory` — serializes shapes
- Updated `applyScene` — hydrates shapes
- Updated `tick` — includes `ShapeSystem.getCollisionSegments(state.shapes)` in active force lines
- Wired `onSelectShape`, `onSelectSegment`, `onDoubleClickShape` callbacks

### CHANGED: `state/sceneManager.js`
- Added shapes to `serializeScene` and `applyScene`
- Clears `selectedShapeId` / `selectedSegmentId` on scene apply

## index.html change required

Add one script tag:

```html
<script src="./engine/textSystem.js"></script>
<script src="./engine/shapeSystem.js"></script>  <!-- ADD THIS LINE -->
<script src="./engine/collision.js"></script>
```

## What was NOT changed
- Line system (state.lines untouched, lines still work independently)
- Text system
- Transport / recording / looping
- MIDI system
- Physics engine
- Swarm system
- UI layout / controls
- Audio system

## Interaction model

| Action | Result |
|---|---|
| Shape tool + click | Creates shape in `state.shapes` |
| Select tool + click shape | Selects shape (white dashed bounds) |
| Select tool + drag shape | Moves entire shape |
| Select tool + double-click shape | Toggles edit mode (gold dashed bounds) |
| Edit mode + click segment | Selects individual segment (gold highlight) |
| Delete key with shape selected | Removes shape |
| Cmd/Ctrl+D with shape selected | Duplicates shape |
| Draw tool | Still creates lines in `state.lines` (unchanged) |

## Constraints preserved
- No segment color overwrite — each segment keeps its own color/note/behavior
- No segment loss — shapes contain all segments created
- Shapes and lines are independent parallel systems
- No groups (not implemented per spec)
- No audio system changes
