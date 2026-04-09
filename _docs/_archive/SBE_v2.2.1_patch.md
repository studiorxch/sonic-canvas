# SBE PATCH — v2.2.3 CRITICAL FIXES

## Scope: Transport + Input Alignment

---

## 1. TRANSPORT STATE (FIX STOP / START TOGGLE)

### Requirements

- STOP must fully halt engine + MIDI
- START must restart cleanly
- Toggle must not get stuck

---

### Add global state

```js
let isPlaying = false;
let loopId = null;
```

---

### Replace transport handlers

```js
function togglePlayback() {
  if (isPlaying) {
    stopPlayback();
  } else {
    startPlayback();
  }
}
```

---

### START

```js
function startPlayback() {
  if (isPlaying) return;

  isPlaying = true;
  loop();
}
```

---

### STOP

```js
function stopPlayback() {
  isPlaying = false;

  if (loopId) {
    cancelAnimationFrame(loopId);
    loopId = null;
  }

  sendAllNotesOff();
}
```

---

### LOOP (CRITICAL GUARD)

```js
function loop() {
  if (!isPlaying) return;

  update(); // physics / collisions / triggers
  render();

  loopId = requestAnimationFrame(loop);
}
```

---

## 2. MIDI HARD STOP (FIX ABLETON BLEED)

### Add:

```js
function sendAllNotesOff() {
  if (!midiOutput) return;

  for (let ch = 0; ch < 16; ch++) {
    midiOutput.send([0xb0 + ch, 123, 0]);
  }
}
```

---

### ALSO guard all triggers

Inside collision / trigger logic:

```js
if (!isPlaying) return;
```

---

## 3. INPUT OFFSET FIX (GLOBAL — REQUIRED)

### Problem

Cursor ≠ canvas position due to scaling mismatch

---

### Add utility

```js
function getCanvasCoords(event, canvas) {
  const rect = canvas.getBoundingClientRect();

  const scaleX = canvas.width / rect.width;
  const scaleY = canvas.height / rect.height;

  return {
    x: (event.clientX - rect.left) * scaleX,
    y: (event.clientY - rect.top) * scaleY,
  };
}
```

---

## 4. APPLY TO ALL INPUT SYSTEMS

### Drawing

```js
const { x, y } = getCanvasCoords(event, canvas);
points.push({ x, y });
```

---

### Shape Placement

```js
const pos = getCanvasCoords(event, canvas);
createShape(pos.x, pos.y);
```

---

### Ball Spawn

```js
const pos = getCanvasCoords(event, canvas);
spawnBall(pos.x, pos.y);
```

---

### Selection

```js
const { x, y } = getCanvasCoords(event, canvas);
```

---

## 5. CANVAS RESOLUTION LOCK (RECOMMENDED)

```js
canvas.width = 1080;
canvas.height = 1920;
```

---

## 6. ACCEPTANCE CRITERIA

### Transport

- STOP halts ALL motion immediately
- Ableton receives NO lingering notes
- START works after STOP

---

### Input

- drawing follows cursor exactly
- shapes appear exactly where clicked
- balls spawn exactly under cursor

---

### System

- no ghost triggers after STOP
- no drift between input and render

---

## FINAL NOTE

Apply this patch before any further feature work.
