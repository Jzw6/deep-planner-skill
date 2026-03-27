# deep-planner

A meta-skill for [OpenClaw](https://openclaw.ai) that enforces structured planning, transparent execution, and self-reflection before tackling complex tasks.

Think of it as the agent's project manager — it plans the work, delegates to the right skills, checks off progress, and catches its own mistakes.

---

## What It Does

When activated, `deep-planner` will:

1. **Parse the request** — identify ambiguities that would derail execution, ask the user once if needed
2. **Generate a TodoList** — a step-by-step plan shown directly in the reply, with tools/skills mapped to each step
3. **Persist the plan** — written to `.todolist/` so it survives context truncation or session restarts
4. **Execute and track** — checks off steps as they complete, syncs the file after each one
5. **Self-reflect** — after finishing, flags anything that was inferred rather than verified

---

## Why This Matters

LLMs tend to jump straight to output without planning. This leads to:
- Skipped steps that only become obvious later
- Hallucinated facts stated with false confidence
- Ambiguous requirements interpreted in the wrong direction
- No recovery path when context gets too long

`deep-planner` fixes this by making the agent **think before it acts**, and keeping that thinking visible and recoverable.

---

## How TodoList Persistence Works

```
Task starts
  → Check .todolist/ for any in-progress file  (resume support)
  → Generate plan → show in reply + write to .todolist/YYYYMMDD-task-name.md

Each step completes
  → Update reply with ✅ checkmark
  → Sync file

Task ends
  → File status set to "completed", kept in place for reference
```

If the conversation context gets truncated mid-task, the agent reads the file on next activation and picks up exactly where it left off.

---

## Skill Structure

```
deep-planner/
├── SKILL.md                        # Core protocol (this is what the agent reads)
└── references/
    └── task-types.md               # Breakdown templates for common task types
```

### Task type templates included

| Type | Description |
|------|-------------|
| `#research` | Research & competitive analysis |
| `#content` | Blog posts, social media, newsletters |
| `#technical` | System design, architecture, tech selection |
| `#data` | Data cleaning, transformation, reporting |
| `#multi-skill` | Chained pipelines across multiple skills |

---

## When It Triggers

The skill activates when:

- Request contains words like: *research, analyze, plan, design, compare, report, strategy*
- Task requires 3+ steps or chains multiple tools/skills
- Key variables are undefined (audience, platform, format, scope)
- Domain is unfamiliar or information may be time-sensitive

---

## What It Delegates

`deep-planner` is a **coordinator, not a worker**. It plans and monitors; it does not generate content, run searches, or execute code directly. Those are delegated:

```
User request
  → [deep-planner] plan + TodoList + write to disk
      → [web search] gather information
      → [domain skill] generate content / process data / etc.
  → [deep-planner] reflect + mark complete
```

---

## Anti-Hallucination Rules

Built into the protocol:

- No fabricated statistics, citations, or examples — must have a source or be labeled as an estimate
- Time-sensitive claims are flagged with knowledge cutoff dates
- Uncertain conclusions are marked ⚠️ and surfaced to the user
- Capability limits are stated honestly rather than papered over

---

## Installation

Install via OpenClaw skill manager or clone this repo and load `deep-planner/` as a local skill.

---

## License

MIT
