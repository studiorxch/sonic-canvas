0408_ENGINE_Phase1_RoadmapClaudeBundle_v1.1.0
Date: 04-08

---

# 🧠 PURPOSE (UPDATED)

This document is the **execution bundle for Claude — Phase 1 (Agent-Centric Revision)**.

It now integrates:
- Agent Profile System (.md-based)
- Agent Clock (local timing)
- Minimal Link System (graph = conductor)

Goal:
Deliver a **stable, predictable, programmable engine** where:
- behavior is data-driven
- agents are defined via .md
- timing is local (no global timeline)

---

# 🎯 PHASE 1 CHECKLIST (UPDATED)

## 🔲 Selection System
- [ ] Single select works
- [ ] Multi-select works
- [ ] Shared properties computed correctly
- [ ] Conflicting values show blank/placeholder
- [ ] Batch updates apply to all selected
- [ ] No UI desync from state

---

## 🔲 Agent Profile System (.md) ✅ NEW CORE

- [ ] Agents load from `/agents/*.md`
- [ ] YAML frontmatter parsed correctly
- [ ] Profiles validated before instantiation
- [ ] Editing .md updates behavior (reload)
- [ ] No hardcoded agents in engine

---

## 🔲 Agent Core System

Each agent must include:
- [ ] mover
- [ ] projector
- [ ] trigger
- [ ] clock (NEW)
- [ ] optional stats

---

## 🔲 Agent Clock (LOCAL TIMING) ✅ NEW

- [ ] interval_ms supported
- [ ] cooldown_ms supported
- [ ] lifespan supported
- [ ] no global timeline dependency
- [ ] triggers gated by clock

---

## 🔲 Audio Mode System

- [ ] audioMode exists on all agents
- [ ] Modes:
  - trigger
  - modulate
  - silent
- [ ] Correct defaults applied
- [ ] Trigger respects audioMode
- [ ] Modulation respects audioMode

---

## 🔲 Mechanics (Still Locked Set)

- [ ] ramp
- [ ] bumper (hard + elastic)
- [ ] field
- [ ] absorber
- [ ] emitter
- [ ] portal

Rules:
- No new mechanics introduced
- Must be data-driven (no hidden behavior)

---

## 🔲 Loop System

- [ ] Emitter produces particles
- [ ] Portal links correctly
- [ ] Portal preserves velocity
- [ ] System runs indefinitely
- [ ] Particle count controlled

---

## 🔲 Minimal Link System (Graph Layer) ✅ NEW

- [ ] Agents can define links in .md
- [ ] Supported modes:
  - trigger
  - delay
- [ ] delay_ms respected
- [ ] propagation works between agents
- [ ] no infinite loop crashes

---

## 🔲 Audio Trigger Layer

- [ ] Collision triggers work
- [ ] Interval triggers work (via clock)
- [ ] Field = modulation only
- [ ] Velocity → volume mapping applied

---

## 🔲 World System (Minimal)

- [ ] gravity only
- [ ] direction vector applied
- [ ] strength applied

---

## 🔲 UI Binding

- [ ] Left: tools + agents
- [ ] Center: canvas
- [ ] Right: inspector

Inspector supports:
- transform
- behavior
- audioMode
- agent timing (interval / cooldown)

---

# ❌ EXCLUDED SYSTEMS (STRICT)

- animation system
- global timeline / conductor timeline
- MIDI sequencing
- ADSR
- scene save/load
- advanced link types (sync, modulation)

---

# 🧱 DATA MODEL (UPDATED)

```ts
type Agent = {
  id: string

  x: number
  y: number
  rotation: number
  scale: number

  movement: string
  speed?: number

  trigger: string
  cooldown_ms?: number
  interval_ms?: number

  audioMode: "trigger" | "modulate" | "silent"
  note?: string

  life?: number

  links?: {
    target: string
    mode: "trigger" | "delay"
    delay_ms?: number
  }[]
}
```

---

# 🧠 CLAUDE DIRECTIVE (UPDATED)

Refactor the engine to support:

1. Agent Profile System (.md-based)
2. Agent Clock (local timing)
3. Minimal Link System (graph propagation)

Constraints:
- Do NOT introduce global timeline
- Do NOT introduce new mechanics
- Do NOT add animation or MIDI
- All behavior must come from profiles

Focus on:
- data-driven agents
- predictable timing
- stable propagation

---

# 🔒 IMPLEMENTATION RULES

1. No hidden state  
2. No implicit behavior  
3. All agents created from .md  
4. No hardcoded timing logic  
5. All timing via AgentClock  
6. Prevent infinite link loops  

---

# 🧪 VALIDATION TESTS (UPDATED)

## Agent Profile Test
- Modify .md → behavior changes on reload

## Loop Test
- System runs 5+ minutes stable

## Timing Test
- Interval agents trigger consistently

## Link Test
- A → B (delay) works correctly
- No runaway triggering

---

# ⚡ IMPLEMENTATION GUIDE

where
- /agents/
- /engine/agentProfile/
- /engine/agentClock/
- /engine/linkSystem/

run
- load profiles → instantiate agents → run loop

expect
- system becomes programmable
- behavior driven by text
- emergent structure appears

---

NEXT PHASE:
- animation system
- MIDI
- advanced links
