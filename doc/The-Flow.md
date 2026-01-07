# The Flow: How GSD Works

GSD (Get Shit Done) is a context engineering system that makes Claude Code reliable for building real software. This document explains how work flows through the system, from initial idea to shipped milestone.

---

## The Core Loop

Every project follows the same fundamental cycle:

```
    +------------------+
    |                  |
    v                  |
Initialize --> Plan --> Execute --> Verify --> Complete
    |                                              |
    |              (repeat for each phase)         |
    +----------------------------------------------+
```

### The Five Stages

| Stage | What Happens | Output |
|-------|--------------|--------|
| **Initialize** | Define vision, extract requirements | PROJECT.md, ROADMAP.md, STATE.md |
| **Plan** | Break phase into executable tasks | PLAN.md files |
| **Execute** | Subagent implements tasks autonomously | Code changes, commits |
| **Verify** | Confirm work meets criteria | SUMMARY.md |
| **Complete** | Archive milestone, prepare next | MILESTONES.md, git tag |

### The Fresh Context Protocol

GSD enforces context hygiene between operations. After each major command, clear your context window before running the next:

```
/gsd:plan-phase 1
[Plan created]
/clear                          <-- Clear context
/gsd:execute-plan .planning/phases/01-foundation/01-01-PLAN.md
```

**Why this matters:** Claude's quality degrades as context fills. Each plan targets ~50% context usage. Starting fresh ensures peak quality for every operation.

---

## Greenfield Workflow (New Projects)

Starting from scratch with just an idea.

### Step 1: Project Initialization

```
/gsd:new-project
```

The system becomes your thinking partner, asking questions to extract:
- What you're building (vision)
- Who it's for (audience)
- What makes it valuable (core value)
- Technical preferences and constraints
- What's explicitly out of scope

**Output:** `.planning/PROJECT.md` capturing your complete vision.

### Step 2: Create Roadmap

```
/gsd:create-roadmap
```

Transforms your vision into actionable phases:
- Breaks work into coherent, deliverable chunks
- Identifies dependencies between phases
- Flags phases that likely need research
- Creates phase directories

**Output:**
- `.planning/ROADMAP.md` (phase breakdown)
- `.planning/STATE.md` (project memory)
- `.planning/phases/` directories

### Step 3: Plan First Phase

```
/gsd:plan-phase 1
```

Creates executable task plans:
- 2-3 tasks per plan (optimal for context)
- Each task has: files, action, verify, done criteria
- Plans are self-contained prompts Claude executes

**Output:** `.planning/phases/01-foundation/01-01-PLAN.md`

### Step 4: Execute

```
/gsd:execute-plan .planning/phases/01-foundation/01-01-PLAN.md
```

The system:
1. Spawns a subagent with fresh 200k context
2. Executes tasks sequentially
3. Commits each task atomically
4. Creates summary of what was built

**Output:** Code, git commits, `01-01-SUMMARY.md`

### Step 5: Repeat and Ship

Continue the plan-execute cycle for each phase until milestone complete:

```
/gsd:plan-phase 2
/gsd:execute-plan ...
...
/gsd:complete-milestone 1.0.0
```

### The Artifact Hierarchy

```
PROJECT.md          <-- Vision (what and why)
    |
ROADMAP.md          <-- Structure (phases and sequence)
    |
STATE.md            <-- Memory (current position, decisions, issues)
    |
PLAN.md             <-- Execution (specific tasks to do now)
    |
SUMMARY.md          <-- History (what happened, what was learned)
```

---

## Brownfield Workflow (Existing Projects)

Already have code? The workflow adapts.

### Step 1: Map the Codebase

```
/gsd:map-codebase
```

Spawns parallel agents to analyze your existing code. Creates `.planning/codebase/` with 7 focused documents:

| Document | What It Captures |
|----------|------------------|
| `STACK.md` | Languages, frameworks, dependencies |
| `ARCHITECTURE.md` | Patterns, layers, data flow |
| `STRUCTURE.md` | Directory layout, where things live |
| `CONVENTIONS.md` | Code style, naming patterns |
| `TESTING.md` | Test framework, test patterns |
| `INTEGRATIONS.md` | External services, APIs |
| `CONCERNS.md` | Tech debt, known issues, fragile areas |

**When to map:**
- First time using GSD on existing project
- After major refactoring
- When codebase has significantly changed

**When to skip:**
- Small projects with obvious structure
- Already mapped recently
- Focused change to well-understood area

### Step 2: Initialize Project

```
/gsd:new-project
```

Same as greenfield, but the system knows your codebase. Questions shift from "What are you building?" to "What are you adding/changing?"

### Step 3: Continue Normally

From here, the workflow is identical:

```
/gsd:create-roadmap
/gsd:plan-phase 1
/gsd:execute-plan ...
```

The codebase documents load automatically during planning. Claude knows your patterns, conventions, and where to put things.

### Brownfield Decision Flow

```
Existing codebase?
      |
      v
  [Yes] -----> /gsd:map-codebase
      |              |
      |              v
      |        Codebase docs exist
      |              |
      +-------> /gsd:new-project
                     |
                     v
              Questions adapt to
              existing context
                     |
                     v
              /gsd:create-roadmap
              (workflow converges)
```

---

## The Planning & Execution Cycle

Each phase goes through a planning pipeline before execution.

### Pre-Planning: Context Gathering

Two optional commands help gather context before planning:

#### `/gsd:discuss-phase [N]`

For when you have a vision but need to articulate it:
- Asks about how you imagine the phase working
- Captures what's essential vs nice-to-have
- Defines explicit scope boundaries
- Creates `{phase}-CONTEXT.md`

**Use when:** You have ideas about look/feel/behavior you want to capture.

#### `/gsd:research-phase [N]`

For niche or complex domains where Claude's training might be stale:
- Researches current ecosystem and best practices
- Identifies standard libraries and patterns
- Documents what NOT to build from scratch
- Creates `{phase}-RESEARCH.md`

**Use when:** Working with 3D graphics, games, audio, shaders, ML, real-time systems, or specialized frameworks.

### Pre-Planning Decision Tree

```
Ready to plan phase?
        |
        v
    Is domain niche/complex?
        |
    +---+---+
    |       |
   Yes      No
    |       |
    v       v
/gsd:research-phase   Do you have specific vision?
    |                       |
    v                   +---+---+
Creates RESEARCH.md     |       |
    |                  Yes      No
    +---+               |       |
        |               v       v
        |      /gsd:discuss-phase    Go direct
        |               |              |
        v               v              v
        +-------> /gsd:plan-phase <----+
```

### Planning: `/gsd:plan-phase [N]`

Generates executable plans:

1. **Reads context** - PROJECT.md, ROADMAP.md, STATE.md, any CONTEXT.md/RESEARCH.md
2. **Loads prior work** - Relevant summaries from completed phases
3. **Breaks into tasks** - 2-3 tasks per plan, ~50% context target
4. **Adds verification** - How to prove each task worked
5. **Commits plan** - Version controlled for history

**Output:** `{phase}-01-PLAN.md`, `{phase}-02-PLAN.md`, etc.

### Execution: `/gsd:execute-plan [path]`

Runs the plan autonomously:

1. **Loads plan** - Reads the PLAN.md file
2. **Executes tasks** - Sequentially, with fresh context
3. **Handles deviations** - Auto-fixes bugs, logs enhancements
4. **Commits atomically** - One commit per task
5. **Creates summary** - Documents what happened
6. **Updates state** - Records decisions, issues, position

**Output:** Code changes, task commits, `{phase}-{plan}-SUMMARY.md`

### The Importance of Clearing Context

```
Plan 1 -----> /clear -----> Execute 1 -----> /clear -----> Plan 2 -----> ...
```

Each major operation gets fresh context. This prevents:
- Quality degradation from context bloat
- Confusion from prior conversation
- The "concise mode" where Claude starts cutting corners

---

## Milestone Management

Milestones mark shipped versions of your project.

### When a Milestone is Complete

All phases in the current milestone have been executed. The system detects this:

```
Plan complete.
Phase 4: Polish COMPLETE - all plans finished.

All 4 phases complete!
This milestone is 100% done.

Next Up: /gsd:complete-milestone
```

### Completing a Milestone

```
/gsd:complete-milestone 1.0.0
```

This ritual:
1. **Verifies completion** - All phases done, work tested
2. **Gathers statistics** - Plans, tasks, files, timeline
3. **Creates MILESTONES.md entry** - Historical record
4. **Evolves PROJECT.md** - Moves shipped requirements to "Validated"
5. **Archives milestone** - Full details to `milestones/v1.0.0-ROADMAP.md`
6. **Creates git tag** - `v1.0.0` for the release
7. **Updates STATE.md** - Fresh start for next milestone

### Starting the Next Milestone

```
/gsd:discuss-milestone    # Figure out what to build next
/gsd:new-milestone "v1.1 Features"    # Create the milestone
```

The new milestone gets:
- Added to ROADMAP.md
- New phase directories created
- STATE.md updated with fresh focus

### Milestone Lifecycle

```
v1.0 MVP                          v1.1 Features
    |                                  |
Phases 1-4                        Phases 5-7
    |                                  |
    v                                  v
/gsd:complete-milestone 1.0.0    /gsd:complete-milestone 1.1.0
    |                                  |
    v                                  v
MILESTONES.md updated            MILESTONES.md updated
Git tag: v1.0.0                  Git tag: v1.1.0
    |                                  |
    +--- /gsd:new-milestone ----->-----+
```

---

## Session Management

GSD maintains state across sessions so you can stop and resume freely.

### Checking Progress

```
/gsd:progress
```

Shows:
- Visual progress bar
- Current phase and plan
- Recent work summary
- Key decisions made
- Open issues
- What's next (with ready-to-run command)

### Pausing Work

```
/gsd:pause-work
```

Creates a handoff file when stopping mid-phase:
- `.continue-here` file with current state
- Updates STATE.md session continuity section
- Captures in-progress work context

### Resuming Work

```
/gsd:resume-work
```

Restores from last session:
- Reads STATE.md for full context
- Shows project status
- Detects incomplete work
- Offers appropriate next action

### STATE.md: The Persistent Memory

STATE.md is the project's living memory:

```markdown
## Project Reference
Core value, current focus

## Current Position
Phase X of Y, Plan A of B
Progress: [██████░░░░] 60%

## Accumulated Context
Decisions made, deferred issues, blockers

## Session Continuity
Last session, where we stopped, resume file
```

This file is:
- Read at the start of every operation
- Updated after every execution
- Compact (under 150 lines) for efficient loading

---

## Workflow Modes

GSD supports two interaction styles, set during `/gsd:new-project`:

### Interactive Mode

- Confirms each major decision
- Pauses at checkpoints for approval
- More guidance throughout
- Best for: Learning GSD, critical projects, uncertain scope

### YOLO Mode

- Auto-approves most decisions
- Executes plans without confirmation
- Only stops for critical checkpoints
- Best for: Experienced users, clear requirements, fast iteration

Change anytime by editing `.planning/config.json`:

```json
{
  "mode": "yolo"
}
```

---

## Decision Points Reference

### When to use `/gsd:discuss-phase`

| Situation | Use Discuss? |
|-----------|--------------|
| You have specific UI/UX ideas | Yes |
| You know what the outcome should look/feel like | Yes |
| Phase scope needs narrowing | Yes |
| Standard CRUD operations | No |
| Clear technical requirements | No |

### When to use `/gsd:research-phase`

| Domain | Use Research? |
|--------|---------------|
| 3D graphics, games | Yes |
| Audio, music, MIDI | Yes |
| Shaders, visual effects | Yes |
| Machine learning | Yes |
| Standard web development | No |
| Well-documented APIs (Stripe, etc.) | No |

### Interactive vs YOLO Mode

| Preference | Choose |
|------------|--------|
| New to GSD | Interactive |
| Exploring requirements | Interactive |
| Critical/production code | Interactive |
| Clear requirements | YOLO |
| Rapid iteration | YOLO |
| Comfortable with GSD | YOLO |

---

## Quick Reference: Command Flow

### New Project (Greenfield)
```
/gsd:new-project
/gsd:create-roadmap
/gsd:plan-phase 1
/gsd:execute-plan [path]
... repeat plan/execute ...
/gsd:complete-milestone 1.0.0
```

### Existing Project (Brownfield)
```
/gsd:map-codebase
/gsd:new-project
/gsd:create-roadmap
... same as greenfield ...
```

### Starting a New Session
```
/gsd:progress          # or /gsd:resume-work
[follow the suggested command]
```

### Adding Urgent Work
```
/gsd:insert-phase 3 "Critical fix"
/gsd:plan-phase 3.1
/gsd:execute-plan [path]
```

### Between Every Major Command
```
/clear                 # Fresh context window
```

---

## The Philosophy

GSD works because it respects how Claude (and LLMs generally) actually function:

1. **Context is finite** - Stay under 50% usage per operation
2. **Fresh context = quality** - Clear between operations
3. **State persists in files** - Not in conversation
4. **Plans are prompts** - Structured for Claude's strengths
5. **Subagents are disposable** - Spawn fresh, discard after

The complexity is in the system. Your workflow stays simple:

```
Describe what you want --> Let GSD extract details --> Execute plans --> Ship
```

That's the flow.
