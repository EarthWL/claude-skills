---
name: kickoff
description: Project kickoff orchestrator — at the start of a project or task, lock the scope before any work begins, then route to the right downstream skills. Runs a structured intake (goal, definition of done, constraints, non-goals, risks) scaled to the task size, refuses to start building until scope and success criteria are confirmed, then recommends the matching skills and auto-advances through them — pausing at human gates — currently debug-mantra, scrutinize, post-mortem, management-talk, design-doc, rollout, incident-response. Invoke ONLY when the user explicitly runs /kickoff or asks to kick off, scope, or plan a new project, feature, or task. Do NOT auto-trigger on ordinary requests, quick questions, casual conversation, or small one-line edits.
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

## Language

Match the user's language. When the user is communicating in Thai, write every user-facing response in **Thai**. Your internal working (reasoning, code, commit messages) may stay in English where natural; code identifiers, file paths, and keys are never translated. The intake questions, scope summary, and hand-off notes all follow the user's language; if `/kickoff` is invoked alone with nothing to infer a language from, ask which language first. Default to the language of the user's most recent message.

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
- **Plan** (the task list — create it with the task tools. Bake the downstream pipeline in as tasks, e.g. design → build → review → ship, and mark which ones are human gates. This list is the pipeline state that drives auto-advance in step 6.)
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
| Live production problem — outage, customer-facing breakage, data loss, security incident | `incident-response` |

This table starts small, on purpose, and grows one skill at a time. Other skills (docx, xlsx, pptx, pdf, python, kissflow-*, nocoly-*, schedule, skill-creator, etc.) are **not** routed yet — they get added one at a time, only after each is verified to behave well as a hand-off target.

If the work falls outside the routed skills above, do it directly with the base tools — do not force-fit it onto a core skill. If two or more match, sequence them (e.g. `design-doc` → `scrutinize` → build → `rollout`, or debug → fix → `post-mortem` → `management-talk`) and say so.

### 6. Hand off and auto-advance

State the plan in one line — "Scope locked. Routing to X, then Y." — and begin.

Then walk the task list **automatically**: when one step completes, advance to the next routed step and invoke its skill *without waiting for a new command*. Build finishing should flow straight into `scrutinize`; a validated fix should flow into `post-mortem`. The task list is the pipeline state — keep it updated as you go.

**Pause and wait for an explicit OK at human gates only:**

- Scope confirmation (step 3) — already cleared before any build.
- Before building from a design — let the user approve the design and any `scrutinize` findings first.
- `rollout` go/no-go — the rollout gate needs the user to set abort criteria and approve exposure.
- Any irreversible or destructive action.
- Posting anything externally (JIRA via `post-mortem` / `management-talk`) — those carry their own sign-off.

Between gates, advance on your own. At a gate, stop, state what's next, and wait. If the user says "step through manually," switch auto-advance off and confirm before each step instead.

### 7. Re-plan mid-flight

The plan is a hypothesis, not a contract. Auto-advance does not mean head-down — when a step's outcome contradicts the plan, stop advancing and re-plan before continuing.

**Re-plan when:**

- A step's result invalidates downstream tasks — `scrutinize` finds the approach is wrong, a chosen design alternative turns out infeasible, a build uncovers a blocker.
- A new requirement, constraint, or risk appears.
- The user changes direction.

**Re-plan loop:**

1. Stop auto-advance — re-planning is itself a pause.
2. Name what changed and which downstream tasks it invalidates.
3. Pick the loop-back point — back to `design-doc` (the how), back to the `kickoff` intake (the scope), or just add / drop / reorder tasks in place.
4. Update the task list so it reflects the new plan — it is the pipeline state.
5. Resume auto-advance from the corrected point.

**Guardrails:**

- **Small in-scope adjustment** → update the tasks and keep going. **Scope or non-goals change** → that's a gate: get the user's OK before widening. Silent scope creep is the failure mode.
- **Thrash guard** — if the plan changes more than about three times, stop tweaking and re-run the intake properly. The scope was wrong, not the tasks.

## Operating rules

- **Match the user's language.** Communicate in whatever language the user wrote in; if `/kickoff` arrives with no text to infer from, ask which language first and wait for the answer.
- **Scope before build, always.** No files, no code, no multi-tool execution before step 3's gate is satisfied.
- **Auto-advance between steps, stop at gates.** Once scope is locked, flow from one routed step to the next without waiting for a new command — but never blow through a human gate (build-from-design approval, `rollout` go/no-go, irreversible actions, external posting).
- **The plan is a hypothesis.** Re-plan the moment a step contradicts it — loop back, update the task list, resume. A scope change is always a gate, and chronic re-planning means re-scope, not re-tweak.
- **Depth matches size.** A small task gets one sentence of scoping, not an interrogation. Over-scoping a tiny task is as much a failure as under-scoping a big one.
- **Name non-goals.** The single most valuable line in any kickoff is what you are *not* doing. Always surface it.
- **Route, don't hoard.** This skill conducts; it doesn't perform. Hand the actual work to the specialist skill and get out of the way.
- **Don't manufacture process.** If the user says "just do it," respect that — confirm the goal in one line and route. The gate is a safeguard, not a toll booth.
- **One correction round is normal; three is a smell.** If scope is still moving on the third pass, ask what specific assumption is wrong instead of re-listing everything.
