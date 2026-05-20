---
name: kickoff
description: Project kickoff orchestrator — at the start of a project or task, lock the scope before any work begins, then route to the right downstream skills. Runs a structured intake (goal, definition of done, constraints, non-goals, risks) scaled to the task size, refuses to start building until scope and success criteria are confirmed, then recommends and hands off to the matching skills (currently debug-mantra, scrutinize, post-mortem, management-talk, design-doc, rollout). Invoke ONLY when the user explicitly runs /kickoff or asks to kick off, scope, or plan a new project, feature, or task. Do NOT auto-trigger on ordinary requests, quick questions, casual conversation, or small one-line edits.
---

# Kickoff

The conductor, not a player. Run this at the **start** of a piece of work to lock the scope, agree on what "done" means, then route the work to the skills that should actually do it.

This skill does not produce the deliverable itself. Its only outputs are: a confirmed scope, a task list, and a decision about which downstream skills to invoke next.

## When to invoke

- `/kickoff`
- "let's start / plan / scope a new project / feature / task"
- "help me kick this off" / "where do I even start with this"

This skill is **manual**. It is invoked deliberately, not automatically.

## When NOT to use

Skip it entirely — do the work directly — when any of these is true:

- A quick factual question, explanation, or piece of casual conversation.
- A single, unambiguous, low-risk change (one-line edit, rename, obvious fix).
- The user already gave a clear goal, success criteria, and scope. Do not re-interrogate what is already settled — confirm in one sentence and move on.

If you catch yourself running a full intake on a 5-minute task, stop. The depth must match the size (see step 0).

## Communication language

Settle this before the intake — it governs every word this skill says back (questions, scope summary, hand-off notes):

- **Mirror the user.** Reply in the same language the user wrote their request in. If they switch languages mid-conversation, switch with them.
- **No text to go on? Ask first.** If `/kickoff` is invoked on its own with nothing else to infer a language from, ask which language to use **before** starting the intake, then proceed in that language.
- Skill names and keywords (`debug-mantra`, `/kickoff`, etc.) stay as-is regardless of language.

## Workflow

Run in order. Do not skip to building.

### 0. Calibrate depth — match the interview to the task size

Read the request and silently classify it. This decides how heavy the rest of the flow is.

| Size | Looks like | Intake depth |
|---|---|---|
| **Small** | One deliverable, clear ask, low risk, < ~15 min | Restate the goal in one line, get a yes, route. No question batch. |
| **Medium** | A few moving parts, some ambiguity, one main deliverable | 2-4 targeted questions, short scope summary, task list. |
| **Large** | Multi-step, multiple deliverables, real unknowns or risk | Full intake, written scope summary, explicit non-goals + risks, task list, routing plan. |

When unsure between two sizes, pick the smaller one and escalate only if the intake surfaces hidden complexity.

### 1. Restate the goal — one sentence, in your own words

State what this work is trying to achieve, in one plain sentence. If you cannot, the request is underspecified — say exactly what is unclear and ask. Do not proceed on a goal you can't articulate.

### 2. Scope intake

For **medium/large** work, ask a focused batch of questions (use the `AskUserQuestion` tool so the user can pick fast). Cover only what's actually unclear:

- **Definition of done** — what does the finished result look like, concretely? How will we know it's right?
- **Audience / user** — who is this for? (changes tone, format, depth)
- **Constraints** — deadline, platform/tools, data sources, budget, must-use or must-avoid tech.
- **Non-goals** — what is explicitly *out* of scope for this round? (the most-skipped, highest-value question)
- **Risks / unknowns** — what could derail this? what do we not know yet?
- **Deliverable format** — file type, channel, where it lives when done.

For **small** work, skip the batch: restate the goal, name the obvious deliverable, and ask for a one-word confirmation.

### 3. Scope gate — refuse to build until this is locked

Do **not** start producing files, code, or multi-step execution until these three are explicit and confirmed by the user:

- [ ] **Goal** — agreed, in one sentence.
- [ ] **Definition of done** — agreed.
- [ ] **Non-goals** — at least named, so scope creep has a fence.

If any is missing, list what's missing and stop. (For small tasks, a single restated sentence the user confirms satisfies all three at once.)

### 4. Write the scope summary + task list

Produce a short, scannable summary the user can correct:

- **Goal** (one sentence)
- **Done when** (the success criteria)
- **Out of scope** (non-goals)
- **Plan** (the task list — create it with the task tools so progress is trackable)
- **Risks** (only real ones; don't manufacture them)

Get an explicit "yes, go" before moving on. One round of correction is normal.

### 5. Route — pick the skills that do the work

Map the confirmed scope to the downstream skill(s). Name them explicitly, say why, then invoke/hand off. Routing is a recommendation to act on, not a rigid pipeline — adjust as the work reveals itself.

| The work is... | Route to |
|---|---|
| Designing how to build something non-trivial, before coding — design doc / RFC / technical design | `design-doc` |
| Debugging — something is broken, throwing, failing, flaky | `debug-mantra` |
| Reviewing a plan / PR / diff / design, or a second opinion | `scrutinize` |
| Writing up a fixed-and-validated bug (RCA / root cause) | `post-mortem` |
| Reporting work up the org — exec / VP / PM, Slack, email, standup | `management-talk` |
| Shipping / releasing / deploying a risky change safely — staged rollout, rollback plan | `rollout` |

This table starts small, on purpose, and grows one skill at a time. Other skills (docx, xlsx, pptx, pdf, python, kissflow-*, nocoly-*, schedule, skill-creator, etc.) are **not** routed yet — they get added one at a time, only after each is verified to behave well as a hand-off target.

If the work falls outside the routed skills above, do it directly with the base tools — do not force-fit it onto a core skill. If two or more match, sequence them (e.g. `design-doc` → `scrutinize` → build → `rollout`, or debug → fix → `post-mortem` → `management-talk`) and say so.

### 6. Hand off

State the plan in one line — "Scope locked. Routing to X, then Y." — and begin. From here, the downstream skill owns the execution.

## Operating rules

- **Match the user's language.** Communicate in whatever language the user wrote in; if `/kickoff` arrives with no text to infer from, ask which language first and wait for the answer.
- **Scope before build, always.** No files, no code, no multi-tool execution before step 3's gate is satisfied.
- **Depth matches size.** A small task gets one sentence of scoping, not an interrogation. Over-scoping a tiny task is as much a failure as under-scoping a big one.
- **Name non-goals.** The single most valuable line in any kickoff is what you are *not* doing. Always surface it.
- **Route, don't hoard.** This skill conducts; it doesn't perform. Hand the actual work to the specialist skill and get out of the way.
- **Don't manufacture process.** If the user says "just do it," respect that — confirm the goal in one line and route. The gate is a safeguard, not a toll booth.
- **One correction round is normal; three is a smell.** If scope is still moving on the third pass, ask what specific assumption is wrong instead of re-listing everything.
