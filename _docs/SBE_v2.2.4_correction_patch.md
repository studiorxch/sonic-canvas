# SBE PATCH — v2.2.4 CORRECTION (DRAWING + INPUT FIX)

## PURPOSE

Fix regressions introduced by previous patch:

- delayed / dashed drawing
- persistent cursor offset
- choppy input
- broken loop separation

---

## 1. REMOVE DRAW QUEUE / DELAY SYSTEM

### DELETE any logic like:

- queued points
- batched input
- delayed processing tied to playback

Example to remove:

queue.push(point)
processLater()

---

## 2. RESTORE DIRECT INPUT DRAWING

### Drawing MUST be event-driven (not loop-driven)

```js
canvas.addEventListener("pointerdown", (e) => {
  isDrawing = true;
  const { x, y } = getCanvasCoords(e, canvas);
  points = [{ x, y }];
});

canvas.addEventListener("pointermove", (e) => {
  if (!isDrawing) return;

  const { x, y } = getCanvasCoords(e, canvas);
  points.push({ x, y });

  drawImmediate(points);
});

canvas.addEventListener("pointerup", () => {
  isDrawing = false;
});
```

---

## 3. IMMEDIATE RENDER (NO RAF DEPENDENCY)

```js
function drawImmediate(points) {
  ctx.beginPath();
  ctx.moveTo(points[0].x, points[0].y);

  for (let i = 1; i < points.length; i++) {
    ctx.lineTo(points[i].x, points[i].y);
  }

  ctx.stroke();
}
```

---

## 4. FIX OFFSET (GLOBAL ENFORCEMENT)

### REQUIRED: Use ONLY this function everywhere

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

### REMOVE ALL INSTANCES OF:

event.clientX  
event.clientY

---

## 5. VERIFY ALL TOOLS USE CORRECT COORDS

Apply getCanvasCoords() to:

- drawing
- shape placement
- ball spawn
- selection / hit detection

---

## 6. LOOP SYSTEM (SIMPLIFY)

```js
function loop() {
  if (!isPlaying) return;

  updatePhysics();
  renderScene();

  loopId = requestAnimationFrame(loop);
}
```

---

## 7. STRICT SEPARATION

### Input Layer:

- drawing
- placing
- selecting

### Simulation Layer:

- physics
- collisions
- audio

---

## RULE

Drawing MUST NOT depend on playback loop.

---

## 8. CLEAR BUTTON (REQUIRED)

```js
function clearScene() {
  objects = [];
  balls = [];
  points = [];

  sendAllNotesOff();
}
```

---

## ACCEPTANCE CRITERIA

- drawing is smooth and immediate
- no delay or dashed artifacts
- cursor alignment is exact
- shapes spawn correctly
- balls spawn correctly
- no offset anywhere

---

## FINAL NOTE

This patch removes incorrect abstraction.

Restore:
DIRECT INPUT → DIRECT OUTPUT

Before adding any new features.
