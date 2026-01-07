# GSD How-To Guide

Quick reference for common tasks. Jump to what you need.

---

## Getting Started

### How to install GSD

```bash
npx get-shit-done-cc
```

The installer prompts for global (`~/.claude/`) or local (`./.claude/`) installation.

**Non-interactive install (Docker, CI, scripts):**

```bash
npx get-shit-done-cc --global   # Install to ~/.claude/
npx get-shit-done-cc --local    # Install to ./.claude/
```

### How to verify installation works

```
/gsd:help
```

Shows the command reference if installed correctly.

### How to switch between interactive and YOLO mode

Edit `.planning/config.json`:

```json
{
  "mode": "yolo"
}
```

**Interactive mode:** Confirms each major decision, pauses at checkpoints.

**YOLO mode:** Auto-approves most decisions, executes without confirmation, only stops for critical checkpoints.

> **Tip:** Mode is set during `/gsd:new-project`. Change anytime by editing config.json.

---

## Starting a Project

### How to start a greenfield project

```
/gsd:new-project
```

The system asks questions until it has everything: goals, constraints, tech preferences, edge cases. Creates `PROJECT.md`.

Then:

```
/gsd:create-roadmap
```

Creates `ROADMAP.md` (phases) and `STATE.md` (living memory).

### How to integrate GSD into an existing codebase

**Step 1:** Map the codebase first:

```
/gsd:map-codebase
```

Spawns parallel agents to analyze your code. Creates `.planning/codebase/` with 7 documents covering stack, architecture, structure, conventions, testing, integrations, and concerns.

**Step 2:** Initialize project:

```
/gsd:new-project
```

Questions focus on what you're adding/changing (not starting from scratch).

**Step 3:** Continue as normal:

```
/gsd:create-roadmap
```

Codebase docs load automatically during planning.

### How to skip codebase mapping

Just run `/gsd:new-project` without mapping first.

**When to skip:**

- Small codebase you can describe in the project brief
- Only adding isolated features that don't touch existing code
- Prototype/spike work where existing patterns don't matter

> **Warning:** Skipping mapping means Claude may miss existing conventions, patterns, or technical debt that affects your work.

---

## Planning Work

### How to create a roadmap

After `/gsd:new-project`:

```
/gsd:create-roadmap
```

Creates:
- `ROADMAP.md` - Phases from start to finish
- `STATE.md` - Memory that persists across sessions

### How to discuss a phase before planning

```
/gsd:discuss-phase 2
```

Use when you have ideas about how something should look or feel. Creates `CONTEXT.md` capturing your vision, essentials, and boundaries.

The system asks about:
- How you imagine it working
- What's essential
- What's out of scope
- Any specific look/feel/behavior

> **Tip:** This is optional. Skip straight to `/gsd:plan-phase` if you're clear on what you want.

### How to research unfamiliar domains

```
/gsd:research-phase 3
```

Use for niche or complex domains where Claude's training may be stale:
- 3D graphics, game development
- Audio/music, shaders
- ML/AI integration
- Real-time systems
- Specialized frameworks

Creates `RESEARCH.md` with:
- Standard stack (what experts use)
- Architecture patterns
- Common pitfalls
- What NOT to hand-roll

> **Skip for commodity work:** Standard auth, CRUD APIs, forms, well-documented integrations.

### How to see what Claude will assume

```
/gsd:list-phase-assumptions 3
```

Shows Claude's intended approach before planning:
- Technical approach (libraries, patterns)
- Implementation order
- Scope boundaries
- Risk areas
- Dependencies

No files created. Lets you course-correct if Claude misunderstood.

---

## Executing Work

### How to execute a plan

```
/gsd:execute-plan .planning/phases/01-foundation/01-01-PLAN.md
```

Or let GSD find the next plan:

```
/gsd:execute-plan
```

Each task runs in a fresh subagent context (200k tokens, zero degradation). Creates `SUMMARY.md` after completion.

> **Tip:** Run `/clear` before executing to start with a fresh context window.

### How to handle checkpoints

Plans include three checkpoint types:

**checkpoint:human-verify (90% of checkpoints)**

Claude automated work; you confirm it's correct.

```
════════════════════════════════════════
CHECKPOINT: Verification Required
════════════════════════════════════════
What I built: [description]
How to verify:
1. Visit https://myapp.vercel.app
2. Check that homepage loads
3. Confirm no console errors

Type "approved" or describe issues
════════════════════════════════════════
```

**checkpoint:decision (9% of checkpoints)**

You choose between options.

```
════════════════════════════════════════
CHECKPOINT: Decision Required
════════════════════════════════════════
Decision: Select authentication provider

Options:
1. supabase - Built-in with DB, free tier
2. clerk - Beautiful UI, best DX
3. nextauth - Free, self-hosted, max control

Select: supabase, clerk, or nextauth
════════════════════════════════════════
```

**checkpoint:human-action (1% - rare)**

Truly unavoidable manual steps with no CLI/API.

```
════════════════════════════════════════
CHECKPOINT: Action Required
════════════════════════════════════════
I automated: Created SendGrid account, requested verification

Need your help: Click the verification link in your email

Type "done" when complete
════════════════════════════════════════
```

### How to deal with authentication gates during execution

When Claude tries to automate and hits an auth error, it creates a dynamic checkpoint:

```
════════════════════════════════════════
CHECKPOINT: Authentication Required
════════════════════════════════════════
I tried to deploy but got authentication error.

What you need to do:
Run: vercel login

Type "done" when authenticated
════════════════════════════════════════
```

This is normal. Authenticate, type "done", and Claude retries automatically.

Common auth gates:
| Service | Auth Command |
|---------|-------------|
| Vercel | `vercel login` |
| Railway | `railway login` |
| Fly | `fly auth login` |
| Supabase | `supabase login` |
| GitHub | `gh auth login` |

### How to handle verification failures

When a task verification fails, execution stops:

```
Verification failed for Task 3: Deploy to production

Expected: curl returns 200
Actual: Connection refused

How to proceed?
1. Retry - Try the task again
2. Skip - Mark as incomplete, continue
3. Stop - Pause execution, investigate
```

Choose based on severity. Skipped tasks are noted in `SUMMARY.md`.

---

## Managing Progress

### How to check where you are

```
/gsd:progress
```

Shows:
- Visual progress bar
- Current phase and plan
- Recent work from SUMMARY files
- Key decisions and open issues
- Offers to execute next plan or create it

### How to pause and resume work

**Pausing mid-phase:**

```
/gsd:pause-work
```

Creates `.continue-here.md` with complete state: current task, completed work, decisions made, blockers, mental context.

**Resuming:**

```
/gsd:resume-work
```

Or just:

```
/gsd:progress
```

Both detect incomplete work and offer to continue.

> **Tip:** GSD auto-commits the handoff file so you can safely close your session.

### How to handle issues logged during execution

During execution, non-critical enhancements get logged to `ISSUES.md` (Rule 5 deviations).

**Review issues:**

```
/gsd:consider-issues
```

Analyzes all open issues against current codebase:
- **Resolved** - Can close (code changed, no longer applicable)
- **Urgent** - Should address now
- **Natural fit** - Good for upcoming phase
- **Can wait** - Keep deferred

Offers batch actions: close resolved, insert urgent phase, note for planning.

---

## Modifying the Roadmap

### How to add a new phase

```
/gsd:add-phase Add admin dashboard
```

Appends a new phase to the end of current milestone. Uses next sequential integer number.

### How to insert urgent work between phases

```
/gsd:insert-phase 5 Fix critical auth bug
```

Creates Phase 5.1 (decimal numbering). Preserves existing phase order while accommodating urgent work.

Multiple insertions stack: 5.1, 5.2, 5.3, etc.

### How to defer work to issues

During execution, enhancements are automatically logged to `ISSUES.md` via deviation Rule 5.

To manually defer something:

1. Add entry to `.planning/ISSUES.md`
2. Use format: `### ISS-XXX: [description]`
3. Include: type, discovered context, effort estimate

Review later with `/gsd:consider-issues`.

---

## Completing Work

### How to complete a milestone

```
/gsd:complete-milestone 1.0.0
```

Verifies all phases complete, then:
- Creates MILESTONES.md entry with stats
- Archives full details to `milestones/` directory
- Creates git tag
- Collapses completed work in ROADMAP.md

### How to start the next version

**Option 1:** After completing milestone:

```
/gsd:discuss-milestone
```

Reviews what shipped, helps identify what's next.

**Option 2:** Create milestone directly:

```
/gsd:new-milestone "v2.0 Features"
```

Creates new milestone section in ROADMAP.md with phases.

### How to review deferred issues

```
/gsd:consider-issues
```

Before starting a new milestone, review what accumulated:
- Close issues that got resolved
- Insert urgent ones as phases
- Note natural fits for upcoming work

---

## Customization

### How to change workflow mode

Edit `.planning/config.json`:

```json
{
  "mode": "interactive"
}
```

Options:
- `"interactive"` - Confirms decisions, pauses at checkpoints
- `"yolo"` - Auto-approves, executes without confirmation

### How to adjust planning depth

Edit `.planning/config.json`:

```json
{
  "depth": "standard"
}
```

Options:
- `"quick"` - 3-5 phases per milestone
- `"standard"` - 5-8 phases per milestone (default)
- `"comprehensive"` - 8-12 phases per milestone

### Understanding config.json gates

```json
{
  "gates": {
    "confirm_project": true,
    "confirm_phases": true,
    "confirm_roadmap": true,
    "confirm_breakdown": true,
    "confirm_plan": true,
    "execute_next_plan": true,
    "issues_review": true,
    "confirm_transition": true
  }
}
```

Each gate controls a confirmation point. Set to `false` to auto-approve.

**Safety gates (always respected):**

```json
{
  "safety": {
    "always_confirm_destructive": true,
    "always_confirm_external_services": true
  }
}
```

These override mode settings for dangerous operations.

---

## Quick Reference

| Task | Command |
|------|---------|
| Start new project | `/gsd:new-project` |
| Map existing code | `/gsd:map-codebase` |
| Create roadmap | `/gsd:create-roadmap` |
| Check progress | `/gsd:progress` |
| Plan a phase | `/gsd:plan-phase 1` |
| Execute plan | `/gsd:execute-plan` |
| Pause work | `/gsd:pause-work` |
| Resume work | `/gsd:resume-work` |
| Add phase | `/gsd:add-phase <description>` |
| Insert urgent work | `/gsd:insert-phase 5 <description>` |
| Complete milestone | `/gsd:complete-milestone 1.0.0` |
| Review issues | `/gsd:consider-issues` |
| Get help | `/gsd:help` |
