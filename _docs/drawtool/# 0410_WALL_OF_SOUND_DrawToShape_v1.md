# 0410_WALL_OF_SOUND_DrawToShape_v1.0.0

Generated: 04/10/2026

---

## 🧠 Assumptions

- Existing draw tool in `drawTools.js`
- Shape system available (`ShapeSystem.createShapeFromPoints`)
- Canvas-based rendering
- Freehand drawing already functional

---

## 🎯 Goal

```
draw → convert → shape → export
```

---

## 🔧 State

```js
state.drawMode = "line"; // "line" | "shape"
```

---

## 🔁 Mode Toggle

```js
window.addEventListener("keydown", (e) => {
  if (e.key === "g") {
    state.drawMode = state.drawMode === "line" ? "shape" : "line";
    console.log("Draw mode:", state.drawMode);
  }
});
```

---

## ✏️ Stroke Capture

```js
let currentStroke = [];

function onPointerDown(pointer) {
  currentStroke = [{ x: pointer.x, y: pointer.y }];
}

function onPointerMove(pointer) {
  currentStroke.push({ x: pointer.x, y: pointer.y });
}
```

---

## 🔗 Stroke → Segments

```js
function strokeToSegments(points) {
  const segments = [];

  for (let i = 0; i < points.length - 1; i++) {
    segments.push({
      type: "line",
      x1: points[i].x,
      y1: points[i].y,
      x2: points[i + 1].x,
      y2: points[i + 1].y,
      color: "#ffffff",
      thickness: 2,
    });
  }

  return segments;
}
```

---

## 📐 Normalize to Local Space

```js
function normalizeSegments(segments) {
  const pts = segments.flatMap((s) => [
    { x: s.x1, y: s.y1 },
    { x: s.x2, y: s.y2 },
  ]);

  const cx = pts.reduce((sum, p) => sum + p.x, 0) / pts.length;
  const cy = pts.reduce((sum, p) => sum + p.y, 0) / pts.length;

  return segments.map((s) => ({
    ...s,
    x1: s.x1 - cx,
    y1: s.y1 - cy,
    x2: s.x2 - cx,
    y2: s.y2 - cy,
  }));
}
```

---

## 🚀 Pointer Up Logic

```js
function onPointerUp() {
  const segments = strokeToSegments(currentStroke);

  if (state.drawMode === "line") {
    createSegmentsBatch(segments);
  }

  if (state.drawMode === "shape") {
    const normalized = normalizeSegments(segments);

    const shape = SBE.ShapeSystem.createShapeFromPoints(
      "custom",
      [normalized],
      { x: currentStroke[0].x, y: currentStroke[0].y },
    );

    state.shapes.push(shape);
    exportShape(normalized);
  }

  currentStroke = [];
}
```

---

## 📤 Export

```js
function exportShape(segments) {
  const output = JSON.stringify(segments, null, 2);

  console.log("=== SHAPE EXPORT ===");
  console.log(output);

  navigator.clipboard.writeText(output).catch(() => {});
}
```

---

## ✨ Optional: Simplify

```js
function simplify(points, step = 3) {
  return points.filter((_, i) => i % step === 0);
}
```

---

## ⚡ Implementation Guide

- **where**
  - `ui/drawTools.js`
  - `main.js`

- **run**
  - press `G` → draw → release

- **expect**
  - shape created + JSON exported
