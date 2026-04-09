0407_UI_LayoutAndContextSystem_v1.2.0

# 🧱 PURPOSE

Unify:

- toolbar (shape + note + color)
- panels (ball, behavior, text)
- layout (non-blocking canvas)

Into a single coherent workspace system

---

# 🧠 CORE PRINCIPLES

1. Canvas Must Never Be Covered  
   UI defines canvas bounds — never overlaps it

2. Toolbar = Instrument State  
   Top of toolbar shows current shape + note + color

3. Color = Note = Identity  
   Each swatch represents a MIDI note (1:1 mapping)

4. No Dead UI  
   If selectable → must be actionable

---

# 🧱 1. LAYOUT SYSTEM

## Structure

<div id="app">
  <div id="sidebar"></div>

  <div id="main">
    <div id="topbar"></div>
    <canvas id="canvas"></canvas>
  </div>
</div>

## CSS

#app {
display: flex;
height: 100vh;
overflow: hidden;
}

#sidebar {
width: 120px;
display: flex;
flex-direction: column;
background: #111;
}

#main {
flex: 1;
display: flex;
flex-direction: column;
}

#topbar {
height: 60px;
flex-shrink: 0;
}

#canvas {
flex: 1;
display: block;
}

HARD RULE:
REMOVE all position:absolute UI panels

---

# 🧱 2. LEFT TOOLBAR

## Current State Indicator

[ ● C# ]

- color-filled circle
- note label inside
- always visible

## Shape Tools

circle, square, triangle, polygon, line, text, ball

## Color / Note Swatches

Each swatch = note + color

---

# 🧬 3. NOTE-COLOR SYSTEM

const NOTE_COLOR_MAP = {
C: "#FF3B30",
"C#": "#FF6B3D",
D: "#FF9F1C",
D#: "#FFD60A",
E: "#A3E635",
F: "#34C759",
"F#": "#2DD4BF",
G: "#22D3EE",
"G#": "#3B82F6",
A: "#6366F1",
"A#": "#A855F7",
B: "#EC4899"
};

Rule:
color is derived from note

---

# 🔁 4. INTERACTION RULES

Swatch Click:

- no selection → sets active note/color
- with selection → applies to all selected shapes

Indicator Sync:
activeNote or selected shape always reflected

---

# 🧱 5. PANELS

All panels must live inside sidebar:

- ball
- behavior
- text

Panels expand/collapse vertically

---

# 🧱 6. CANVAS RESIZE

canvas.width = canvas.clientWidth
canvas.height = canvas.clientHeight

---

# 🧱 7. CLEANUP

Remove:

- floating color panel
- MIDI grid
- overlapping UI

---

# 🧪 8. ACCEPTANCE TEST

- Resize window → canvas resizes
- No UI overlaps canvas
- Swatch updates indicator
- Shape creation matches indicator
- Selection updates indicator
- Multi-select updates all shapes
- Panels stay inside sidebar

---

# ⚠️ CONSTRAINTS

DO NOT:

- modify shape system
- modify audio system

ONLY:

- restructure layout
- unify toolbar + swatches

---

# ⚡ IMPLEMENTATION GUIDE

where:

- index.html
- CSS layout
- toolbar component

expect:

- stable layout
- clear state
- instrument-like UX
