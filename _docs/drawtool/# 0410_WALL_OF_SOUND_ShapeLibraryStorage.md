# 0410_WALL_OF_SOUND_ShapeLibraryStorage_v1.0.0

Generated: 04/10/2026

---

## 🧠 Strategy

```
localStorage = working memory
JSON export = source of truth
```

---

## 📦 Structure

```js
const SHAPE_LIBRARY = {
  name: [segments],
};
```

---

## 💾 Save

```js
function saveLibrary(library) {
  localStorage.setItem("sbe_shape_library", JSON.stringify(library));
}
```

---

## 📂 Load

```js
function loadLibrary() {
  const raw = localStorage.getItem("sbe_shape_library");
  return raw ? JSON.parse(raw) : {};
}
```

---

## ➕ Add Shape

```js
function addShapeToLibrary(name, segments) {
  const library = loadLibrary();
  library[name] = segments;
  saveLibrary(library);
}
```

---

## 🔗 Hook Export

```js
function exportShape(segments) {
  const name = prompt("Shape name?");
  if (!name) return;

  addShapeToLibrary(name, segments);
}
```

---

## 🧱 Populate UI

```js
const userShapes = loadLibrary();

Object.entries(userShapes).forEach(([name, segments]) => {
  addShapeButton(name, segments);
});
```

---

## 📥 Download

```js
function downloadLibrary() {
  const data = localStorage.getItem("sbe_shape_library");

  const blob = new Blob([data], { type: "application/json" });
  const url = URL.createObjectURL(blob);

  const a = document.createElement("a");
  a.href = url;
  a.download = "sbe_shapes.json";
  a.click();
}
```

---

## 📤 Import

```js
function importLibrary(file) {
  const reader = new FileReader();

  reader.onload = () => {
    const data = JSON.parse(reader.result);
    saveLibrary(data);
    location.reload();
  };

  reader.readAsText(file);
}
```

---

## ⚡ Implementation Guide

- **where**
  - `state/shapeLibrary.js`

- **run**
  - save shape → reload

- **expect**
  - shapes persist + reusable
