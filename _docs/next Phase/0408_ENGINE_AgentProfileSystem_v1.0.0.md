0408_ENGINE_AgentProfileSystem_v1.0.0
Date: 04-08

---

# 🧠 PURPOSE

Define a Markdown-based system for creating, editing, and loading Agents.

Agents are configured via `.md` files using YAML frontmatter.

Goal:
- Human-readable
- Machine-parseable
- Fast iteration
- Claude-compatible

---

# 📂 DIRECTORY STRUCTURE

/agents/
  pad_01.md
  bumper_01.md
  ambient_orb.md

---

# 🧩 PROFILE FORMAT

Each agent is defined as:

- YAML frontmatter (required)
- Optional Markdown notes

---

## Example

```md
---
id: pad_01

# CORE
type: agent
active: true

# MOVER
movement: vertical
speed: 0.2
rotation_range: 0
anchor: center

# PROJECTOR
projector: shape
shape: square
size: 0.1

# TRIGGER
trigger: collision
cooldown_ms: 200

# CLOCK
interval_ms: null
lifespan: 60

# STATS
life: 100

# AUDIO
audio_type: percussive
note: C3

# LINKS
links:
  - target: agent_02
    mode: delay
    delay_ms: 200
---

# Notes

Vertical pad used for rhythmic bounce testing.
```

---

# 🧱 SCHEMA (PARSED OBJECT)

```ts
type AgentProfile = {
  id: string;
  type?: "agent";

  active?: boolean;

  movement: "all" | "horizontal" | "vertical" | "static";
  speed?: number;
  rotation_range?: number;
  anchor?: "center" | "edge";

  projector: "particle" | "shape" | "field";
  shape?: string;
  size?: number;

  trigger: "collision" | "interval" | "manual";
  cooldown_ms?: number;

  interval_ms?: number;
  lifespan?: number;

  life?: number;

  audio_type?: string;
  note?: string;

  links?: {
    target: string;
    mode: "trigger" | "delay";
    delay_ms?: number;
  }[];
};
```

---

# ⚙️ PARSING SYSTEM

## Dependency

```bash
npm install gray-matter
```

---

## Loader

```ts
import matter from "gray-matter";

export function parseAgentProfile(md: string) {
  const { data } = matter(md);
  return data;
}
```

---

## Factory

```ts
export function createAgentFromProfile(profile: AgentProfile) {
  return {
    id: profile.id,
    position: { x: 0, y: 0 },

    mover: createMover(profile),
    projector: createProjector(profile),
    trigger: createTrigger(profile),
    stats: createStats(profile),
    clock: createClock(profile),

    links: profile.links || []
  };
}
```

---

# 🧠 VALIDATION RULES

- id must exist
- movement must be valid enum
- trigger must be valid enum
- speed must be >= 0
- cooldown_ms >= 0
- interval_ms >= 0

Reject invalid profiles.

---

# 🔁 LIVE RELOAD (OPTIONAL)

- watch `/agents/` directory
- re-parse on change
- hot-update agents

---

# 🎯 PHASE 1 SCOPE

Required:
- movement
- projector
- trigger
- cooldown OR interval

Optional:
- audio
- links
- stats

---

# ❌ EXCLUDED

- scripting inside profiles
- nested logic
- dynamic expressions

---

# 🧠 RESULT

You now have:

- programmable agents
- editable behavior via text
- shareable system units
- Claude-compatible generation

---

# ⚡ IMPLEMENTATION GUIDE

where
- /src/engine/agentProfile/

run
- load .md → parse → create agent

expect
- edit file → reload → behavior changes
