0408_ENGINE_SelectionAudioPatch_v1.0.0

# 🧠 Scope

This patch introduces two high-impact system upgrades:

1. Selection System (true multi-select with shared state)
2. Audio Mode Layer (trigger / modulate / silent)

These sit on top of your existing engine — no rewrites.

---

# ⚙️ Assumptions

- React + TypeScript environment
- Existing:
  - Shape / Mechanic / Particle types
  - Selection already exists (basic)
  - Audio trigger system exists
- File structure:

/engine
/ui
/types

---

# 🧩 1. TYPE SYSTEM (FOUNDATION)

## /types/selection.ts

```ts
export type SelectionType = "none" | "single" | "multi"

export interface SelectionContext {
  ids: string[]
  type: SelectionType
  shared: Record<string, any> // only shared props across selection
}
```

---

## Extend Shape / Mechanic

```ts
export type AudioMode = "trigger" | "modulate" | "silent"

export interface BaseObject {
  id: string
  type: string
  audioMode?: AudioMode
}
```

---

# 🧠 2. SELECTION ENGINE

## /engine/selection.ts

```ts
import { SelectionContext } from "../types/selection"

type AnyObject = Record<string, any>

/**
 * Computes shared properties across selected objects
 * Only returns keys where ALL values match
 */
export function computeSharedProps(objects: AnyObject[]): Record<string, any> {
  if (objects.length === 0) return {}

  const base = objects[0]
  const shared: Record<string, any> = {}

  for (const key of Object.keys(base)) {
    const value = base[key]

    const isShared = objects.every(obj => obj[key] === value)

    if (isShared) {
      shared[key] = value
    }
  }

  return shared
}

/**
 * Builds selection context
 */
export function buildSelectionContext(
  ids: string[],
  allObjects: AnyObject[]
): SelectionContext {
  if (ids.length === 0) {
    return { ids: [], type: "none", shared: {} }
  }

  const selected = allObjects.filter(obj => ids.includes(obj.id))

  const type = ids.length === 1 ? "single" : "multi"

  return {
    ids,
    type,
    shared: computeSharedProps(selected)
  }
}
```

---

# 🎛 3. APPLY PATCH TO MULTI-SELECTION

## /engine/updateSelection.ts

```ts
/**
 * Applies property updates to all selected objects
 */
export function applySelectionUpdate<T>(
  objects: T[],
  selectedIds: string[],
  patch: Partial<T>
): T[] {
  return objects.map(obj => {
    if (!selectedIds.includes((obj as any).id)) return obj

    return {
      ...obj,
      ...patch
    }
  })
}
```

---

# 🎛 4. UI BINDING (CRITICAL)

## /ui/hooks/useSelection.ts

```ts
import { useMemo } from "react"
import { buildSelectionContext } from "../../engine/selection"

export function useSelection(ids: string[], objects: any[]) {
  return useMemo(() => {
    return buildSelectionContext(ids, objects)
  }, [ids, objects])
}
```

---

## Example Panel Logic

```ts
const selection = useSelection(selectedIds, objects)

// show only shared props
const strength = selection.shared.strength ?? null
```

---

## Guard UI Inputs

```ts
const isMulti = selection.type === "multi"

<input
  value={strength ?? ""}
  placeholder={isMulti ? "—" : ""}
  onChange={(e) =>
    updateSelection({ strength: Number(e.target.value) })
  }
/>
```

---

# 🔊 5. AUDIO MODE SYSTEM

## /engine/audioMode.ts

```ts
import { AudioMode } from "../types/selection"

/**
 * Determines if object should emit sound
 */
export function shouldTriggerAudio(mode?: AudioMode): boolean {
  return mode === "trigger"
}

/**
 * Determines if object modulates sound
 */
export function shouldModulateAudio(mode?: AudioMode): boolean {
  return mode === "modulate"
}
```

---

# 🔊 6. PATCH AUDIO TRIGGERS

## BEFORE (likely)

```ts
triggerSound(note)
```

---

## AFTER (safe + modular)

```ts
import { shouldTriggerAudio } from "./audioMode"

function handleCollision(obj: any, velocity: number) {
  if (!shouldTriggerAudio(obj.audioMode)) return

  // velocity → volume mapping
  const volume = Math.min(1, velocity / 10)

  triggerSound(obj.note, volume)
}
```

---

# 🌊 7. MODULATION LAYER (FIELDS, ETC)

```ts
import { shouldModulateAudio } from "./audioMode"

function applyFieldEffect(obj: any, particle: any) {
  if (!shouldModulateAudio(obj.audioMode)) return

  // subtle ambient modulation
  const modulation = particle.velocity * 0.05

  modulateGlobalFilter(modulation)
}
```

---

# 🧱 8. DEFAULT BEHAVIOR RULES

Apply on object creation:

```ts
function assignDefaultAudioMode(type: string): AudioMode {
  switch (type) {
    case "ramp":
      return "silent"

    case "field":
      return "modulate"

    case "bumper":
    case "emitter":
    case "portal":
      return "trigger"

    default:
      return "silent"
  }
}
```

---

# 🎛 9. UI CONTROL (RIGHT PANEL)

Add dropdown:

```ts
<select
  value={selection.shared.audioMode ?? ""}
  onChange={(e) =>
    updateSelection({ audioMode: e.target.value })
  }
>
  <option value="trigger">Trigger</option>
  <option value="modulate">Modulate</option>
  <option value="silent">Silent</option>
</select>
```

---

# 🧠 RESULT

After this patch:

## You unlock:

- true multi-object editing
- safe UI behavior (no conflicts)
- silent infrastructure vs sound objects
- modulation-based ambience
- velocity-sensitive sound

---

# 🔥 What Changes Visually

- selecting 5 ramps → shows shared props only
- selecting ramp + bumper → conflicting fields blank
- fields subtly affect sound instead of triggering
- system becomes readable + intentional

---

# ⚡ Implementation Guide

where
- /engine/selection.ts
- /engine/audioMode.ts
- /ui/hooks/useSelection.ts

run
- integrate into existing selection + audio trigger flow

expect
- immediate UX upgrade
- cleaner sound behavior
- system feels “designed” instead of reactive

---

When you’re ready:

next step is “Mechanics Animation + Time System”

That’s where this turns into a real instrument.
