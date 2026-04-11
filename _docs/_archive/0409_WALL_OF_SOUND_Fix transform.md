We need to fix shape transform + duplication issues in the current app.

Context:

- Shapes exist in state.shapes with segments[]
- Bounding boxes are rendering correctly
- Transform system exists (translateSelection)
- shapeTransforms.js is already loaded

DO NOT redesign anything.
DO NOT add new systems.
DO NOT touch mechanics beyond preserving them.

---

## FIX 1 — Duplicate must preserve mechanic + behavior

Problem:
Duplicated shapes lose mechanicType and/or sound config.

Fix:

- In duplicateShape:
  - ensure each segment preserves:
    - mechanicType
    - behavior
    - midi
    - style
  - after copying each segment, call:
    rebuildSoundConfig(segment)

Result:

- duplicated shapes behave identically to original

---

## FIX 2 — clearScene must remove shapes

Problem:
Clearing the canvas leaves shapes behind.

Fix:

- In clearScene(), add:
  state.shapes = [];

Do not modify anything else in clearScene.

---

## FIX 3 — Rotation and Scale not working

Problem:
R + drag and S + drag do nothing.

Root cause:
translateSelection() does not check held keys.

Fix:

1. Ensure heldKeys exists:
   - Set tracks keydown / keyup
   - keys stored as lowercase

2. In translateSelection():

Replace shape branch with:

- if shape is selected AND no segment selected:
  - if heldKeys has "r":
    rotateShape(shape, dx \* 0.01)
  - else if heldKeys has "s":
    scaleShape(shape, 1 + dx \* 0.01)
  - else:
    translateShape(shape, dx, dy)

3. Do not modify segment drag behavior

Result:

- R + drag → rotate
- S + drag → scale
- normal drag → move

---

## FIX 4 — Minimal visual feedback (NO redesign)

Add ONLY:

- Draw small corner points on bounding box (4 corners)
- Draw one rotation indicator above box (top center)

Do NOT redesign renderer.
Do NOT change layout.

---

## CONSTRAINTS

- Do not modify:
  - mechanics system
  - eventBus
  - collision
- Do not refactor architecture
- Only fix the issues listed above

---

## OUTPUT FORMAT

Return:

1. Exact code changes
2. File names
3. Minimal explanation per change
