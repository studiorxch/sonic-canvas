# 0410_WALL_OF_SOUND_ShapePanel_v1.0.0

Generated: 04/10/2026

---

## 🧠 Purpose

Provide a fast, visual interface for:

- selecting shapes
- saving custom shapes
- managing a personal shape library

This panel is a **core instrument surface**, not just UI.

---

## 🎯 Design Principles

- visual first (not text-heavy)
- fast selection (one-click deploy)
- separation of:
  - system shapes
  - user shapes

- minimal friction for saving + reuse

---

## 🧱 Layout

```txt
[ SHAPES ]

— SYSTEM —
[ ◯ ] [ ◻ ] [ ⌁ ] [ ⭕ ]

— YOUR SHAPES —
[ ◇ ] [ ~ ] [ ⌁⌁ ] [ custom ]

[ Save Current ]
[ Export ] [ Import ]
```

---

## 🧩 HTML Structure

```html
<aside id="shape-panel">
  <div class="shape-section">
    <p>SYSTEM</p>
    <div id="system-shapes" class="shape-grid"></div>
  </div>

  <div class="shape-section">
    <p>YOUR SHAPES</p>
    <div id="user-shapes" class="shape-grid"></div>
  </div>

  <div class="shape-actions">
    <button id="save-shape">Save Current</button>
    <button id="export-shapes">Export</button>
    <input type="file" id="import-shapes" />
  </div>
</aside>
```

---

## 🎨 Shape Button (Thumbnail-Based)

### Core Idea

Each shape renders into a **mini canvas preview**, not text.

---

## 🔲 Thumbnail Renderer

```js
function renderShapeThumbnail(segments, size = 48) {
  const canvas = document.createElement("canvas");
  canvas.width = size;
  canvas.height = size;

  const ctx = canvas.getContext("2d");

  ctx.translate(size / 2, size / 2);
  ctx.scale(0.5, 0.5);

  ctx.strokeStyle = "#ffffff";
  ctx.lineWidth = 2;

  segments.forEach((seg) => {
    ctx.beginPath();
    ctx.moveTo(seg.x1, seg.y1);
    ctx.lineTo(seg.x2, seg.y2);
    ctx.stroke();
  });

  return canvas;
}
```

---

## 🔘 Shape Button Generator

```js
function addShapeButton(name, segments, container) {
  const btn = document.createElement("button");
  btn.className = "shape-button";

  const preview = renderShapeThumbnail(segments);
  btn.appendChild(preview);

  btn.title = name;

  btn.onclick = () => {
    createShapeAtCenter(segments);
  };

  container.appendChild(btn);
}
```

---

## 🧠 Populate Panel

```js
function populateShapePanel() {
  const systemContainer = document.getElementById("system-shapes");
  const userContainer = document.getElementById("user-shapes");

  systemContainer.innerHTML = "";
  userContainer.innerHTML = "";

  // System shapes
  Object.entries(SHAPE_LIBRARY).forEach(([name, segments]) => {
    addShapeButton(name, segments, systemContainer);
  });

  // User shapes
  const userShapes = loadLibrary();
  Object.entries(userShapes).forEach(([name, segments]) => {
    addShapeButton(name, segments, userContainer);
  });
}
```

---

## 💾 Save Current Shape

```js
document.getElementById("save-shape").onclick = () => {
  const shape = getSelectedShape();
  if (!shape) return;

  const name = prompt("Shape name?");
  if (!name) return;

  addShapeToLibrary(name, shape.segments);
  populateShapePanel();
};
```

---

## 📤 Export

```js
document.getElementById("export-shapes").onclick = () => {
  downloadLibrary();
};
```

---

## 📥 Import

```js
document.getElementById("import-shapes").onchange = (e) => {
  const file = e.target.files[0];
  if (!file) return;

  importLibrary(file);
};
```

---

## 🎨 Styling (Minimal)

```css
.shape-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 6px;
}

.shape-button {
  width: 56px;
  height: 56px;
  background: #111;
  border: 1px solid #333;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}

.shape-button:hover {
  border-color: #fff;
}
```

---

## 🔥 Key Behavior

- click → instantly places shape
- save → persists to library
- reload → shapes restored
- thumbnails = visual recognition (no guessing)

---

## 🚀 Future Upgrades

- hover animation preview
- rename / delete shapes
- drag-to-canvas placement
- tagging (dense / sparse / rhythmic)
- color preview per segment

---

## ⚡ Implementation Guide

- **where**
  - `index.html` (shape panel)
  - `ui/shapePanel.js` (new)
  - `state/shapeLibrary.js`

- **run**
  - load panel → save shape → see thumbnail

- **expect**
  - visual shape library
  - faster workflow
  - stronger identity in compositions

---
