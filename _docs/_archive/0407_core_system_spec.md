# 0407_CORE_SYSTEM_SPEC_v1.0.0

Generated: 04/07/2026

---

## 🧱 0407_CORE_ShapesSelectionSystem_v1.0.0

### Purpose
Define stable selection + interaction model for shapes.

### Rules
- Shape = primary selectable unit
- Segment = hidden unless editing
- Group = transform container

### Interaction
- Click → select shape
- Shift + click → multi-select
- Drag → move
- Double click → edit mode

---

## 🧱 0407_GROUPS_MultiVoiceShapes_v1.0.0

### Purpose
Support shapes with multiple musical voices.

### Key Rule
Each segment retains:
- color
- note
- behavior

### Group Behavior
- Groups do NOT flatten shapes
- Groups ONLY apply transforms

---

## 🧱 0407_SHAPES_SegmentsGroupingSpec_v1.1.0

### Data Model

```ts
type Vec2 = { x: number; y: number; };

type Segment = {
  id: string;
  type: "line" | "arc";
  start?: Vec2;
  end?: Vec2;
  center?: Vec2;
  radius?: number;
  startAngle?: number;
  endAngle?: number;
  color: string;
  note?: string;
  behavior?: "attract" | "repel" | "orbit";
};

type Shape = {
  id: string;
  kind: string;
  position: Vec2;
  rotation: number;
  scale: number;
  segments: Segment[];
  isExpanded: boolean;
};

type Group = {
  id: string;
  children: (Shape | Group)[];
  position: Vec2;
  rotation: number;
};
```

### Transform Flow
Group → Shape → Segment

### Constraints
- No segment loss
- No color overwrite
- No note overwrite

---

## 🧱 0407_AUDIO_ResponsibilityBoundary_v1.0.0

### Current Model

Segment → note → MIDI system

### Do NOT build yet
- synth engine
- routing UI
- export system

### Focus
- geometry stability
- interaction clarity

---

## Final Principle

System priority:

1. Geometry stability
2. Interaction clarity
3. Musical identity preservation
4. THEN audio expansion

