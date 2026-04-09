# SBE v2.2.2 — OUTPUT + LOOP + PLAYABLE CANVAS (MASTER SPEC)

## OBJECTIVE

Make SBE **playable and intuitive** by fixing drawing, text, and interaction before scaling features.

Focus:
- bar-based recording + loop system
- export (video + audio)
- FIXED drawing precision
- freehand drawing with smoothing
- simple text tool (on-canvas)
- shape library for instant usability

---

## CORE SYSTEM

DRAW → PLAY → RECORD → LOOP → EXPORT

---

## OUTPUT STACK

1. LOOP (in-app playback)  
2. VIDEO (.webm)  
3. AUDIO (.wav)  
4. SCENE (.json)  
5. IMAGE (.png)  

---

## 1. TRANSPORT

- BPM-based timing
- 4/4 default
- recording in bars (8 / 16 / 32)

---

## 2. RECORDING + LOOP

- quantized start (next bar)
- fixed bar duration
- auto loop playback after recording
- re-record overwrites loop

---

## 3. DRAWING SYSTEM (CRITICAL FIX)

### 3.1 Cursor Alignment (REQUIRED)

Fix coordinate mapping:

const rect = canvas.getBoundingClientRect()

const x = (event.clientX - rect.left) * (canvas.width / rect.width)
const y = (event.clientY - rect.top) * (canvas.height / rect.height)

Result:
- cursor matches draw point exactly
- fixes BOTH drawing + ball placement

---

### 3.2 Freehand Drawing Tool

Replace line tool with:

FREEHAND DRAW

Behavior:
- user draws freely
- points captured continuously
- stroke is smoothed after input

---

### 3.3 Stroke Smoothing

Apply lightweight smoothing:

Options:
- Chaikin smoothing (preferred)
- point reduction (simplification)

Result:
messy input → clean shape

---

## 4. SHAPE LIBRARY (NEW)

Provide preset shapes:

Containers:
- circle
- square
- triangle
- rounded loop

Paths:
- zig zag
- sine
- straight

Forms:
- X, V, L, M, C

Shapes act as:
RHYTHM TEMPLATES

---

## 5. TEXT TOOL (REBUILT)

Behavior:

- click on canvas → type immediately
- no panel dependency
- live editing

After placement:

- move
- scale
- rotate

NOT INCLUDED:

- font panel
- kerning
- advanced typography

---

## 6. BALL TOOL FIXES

- balls spawn EXACTLY at cursor
- balls can be selected
- balls can be deleted

---

## 7. NOTE COLOR SYSTEM

- each MIDI note = fixed color
- color always visible on object
- no customization (yet)

---

## 8. EXPORT SYSTEM

- video: canvas capture
- audio: Web Audio capture
- scene: JSON
- image: PNG

---

## 9. UI (MINIMAL)

Tools:
- Select
- Draw
- Shape
- Text
- Ball

Controls:
[ RECORD ]
[ STOP ]

After recording:
[ EXPORT ]
[ RETAKE ]

---

## CONSTRAINTS

- no precision-only tools
- no panel-heavy workflows
- no second-based timing

---

## ACCEPTANCE CRITERIA

- drawing feels natural
- cursor alignment is correct
- shapes produce usable rhythms
- text works directly on canvas
- loop playback is seamless
- export works (video + audio)

---

## RESULT

Playable Canvas  
+ Loop Instrument  
+ Content Generator  
