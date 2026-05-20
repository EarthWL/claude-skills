---
name: design-doc
description: Design-doc / RFC discipline in the Google design-doc style — before building anything non-trivial, force the design onto the page — problem statement, goals and non-goals, at least two alternatives considered (including doing nothing), the chosen approach with its tradeoffs, and cross-cutting concerns (security, performance, rollout, testing, failure modes). Refuses to jump to a proposed solution until the problem and real alternatives are written down, then produces a design doc and hands off to scrutinize for review. Invoke ONLY when the user explicitly runs /design-doc or asks to write a design doc, RFC, or technical design. Do NOT auto-trigger on ordinary requests, quick questions, casual conversation, bug fixes, or small edits.
---

# Design Doc

Write the design before the code. The deliverable is a document, but the **point** is the thinking it forces: stating the real problem and weighing real alternatives *before* committing to a build. The most common engineering failure this prevents is shipping the first idea that came to mind.

This is the Google design-doc shape: problem → goals/non-goals → alternatives → chosen approach → tradeoffs → cross-cutting concerns → risks.

## When to invoke

- `/design-doc`
- "write a design doc / RFC / technical design for ..."
- "how should we build X" when X is non-trivial and worth thinking through first

This skill is **manual** — invoked deliberately, not automatically.

## When NOT to use

- A trivial or obvious change (one-liner, rename, config tweak). The PR description is the record.
- A bug. Fixing one is `debug-mantra`; recording a fixed one is `post-mortem`.
- A pure factual or research question.
- A design that already exists and is agreed — don't re-litigate settled decisions.

## Language

Match the user's language. When the user is communicating in Thai, write every user-facing response in **Thai**. Your internal working (reasoning, code, commit messages) may stay in English where natural; code identifiers, file paths, and keys are never translated. Write the design doc in the user's language too, unless the user says its readers use another language; if `/design-doc` is invoked alone with nothing to infer from, ask first. Default to the language of the user's most recent message.

## Workflow

Run in order. Do not skip ahead to the solution.

### 0. Calibrate depth — match the doc to the size

| Size | Looks like | Doc weight |
|---|---|---|
| **Small** | One component, low risk, a day or two of work | One-pager: problem, chosen approach, 2 alternatives in a line each, key risk. |
| **Medium** | Several moving parts, some unknowns, one main system | Standard doc, all sections, brief cross-cutting. |
| **Large** | New system, cross-team, migration, or high blast radius | Full doc + explicit rollout, failure modes, and cross-cutting analysis. |

When unsure, pick the smaller weight and expand only if the design surfaces real complexity.

### 1. Problem first — state it before any solution

What is broken or missing, who feels it, and what evidence shows it is real and worth solving? If you cannot state the problem crisply in a few sentences, stop and get it — a design doc for a vague problem is wasted motion.

### 2. Goals and non-goals

- **Goals** — what success looks like, measurable where possible.
- **Non-goals** — what this design explicitly does *not* try to do. The most-skipped, scope-protecting section.

### 3. Alternatives — at least two, and one of them is "do nothing"

This is the heart of the doc and the gate below. For each alternative: a short sketch of the approach, and the honest reason it is or isn't chosen. "Do nothing / a smaller change" is always on the list — if the status quo is acceptable, the most valuable output is saying so.

### 4. Chosen approach + tradeoffs

How the chosen design works — components, data flow, interfaces, key decisions. Then state plainly what it gives up. Every design has a cost; if you can't name the cost, you haven't understood the design.

### 5. Cross-cutting concerns

Cover the ones that apply; skip the ones that don't (say which you skipped and why):

- Security / privacy / data handling
- Performance / cost / scale
- Failure modes — what happens when each part breaks
- Observability — how we'll know it's working
- Testing — how the design will be verified
- Migration / rollout / backward compatibility

### 6. Risks & open questions

Real unknowns, decisions still pending, and who needs to decide them. Be honest — an open question on the page is information; a hidden one is a future incident.

### 7. Produce the doc + hand off

- Produce the design doc as a Markdown document by default (the user can save or share it). Confirm the destination if it matters.
- Then route to `scrutinize` for an outsider review **before** any build begins. The author should not be the only reviewer.

## Design-doc gate — refuse to write the "Proposed design" until all of these are on the page

- [ ] **Problem** stated clearly — who, what, evidence it's real.
- [ ] **Goals + non-goals** named.
- [ ] **At least two alternatives** considered, one of which is "do nothing / a smaller change."

If any is missing, list what's missing and stop. Do not draft a solution to an unstated problem.

## Template

```
# <Title>
Author · Date · Status: Draft | In review | Approved · Reviewers

1. Context        — background; why this, why now
2. Problem        — what's broken/missing, who feels it, evidence
3. Goals          — measurable success
4. Non-goals      — explicitly out of scope
5. Proposed design — chosen approach: components, data flow, interfaces
6. Alternatives considered — 2+, including "do nothing", each with why-not
7. Tradeoffs      — what the chosen design gives up
8. Cross-cutting  — security, performance, failure modes, observability, testing, rollout
9. Risks & open questions — unknowns + who decides
10. Rollout / milestones (optional)
11. References
```

## Where it fits

`/kickoff` locks the **scope** (what + done-criteria) → `/design-doc` decides **how** (this skill) → `scrutinize` reviews it from the outside → then build. Use them as a chain; each hands the next a cleaner starting point.

## Operating rules

- **Match the user's language.** Communicate in whatever language the user wrote in; if `/design-doc` arrives with no text to infer from, ask which language first and wait.
- **Problem before solution, always.** No proposed design before the gate is satisfied.
- **"Do nothing" is always a candidate.** The simplest design is often no new code; force yourself to rule it out explicitly.
- **At least two real alternatives.** Strawmen you set up only to knock down don't count — give each a fair sketch.
- **Name non-goals.** A design without a fence around it invites scope creep.
- **Honest tradeoffs.** If you can't state what the design costs, you don't understand it yet.
- **Scale to size.** Don't manufacture a ten-page doc for a one-day change; don't one-page a system migration.
- **Hand to `scrutinize` before building.** The author is the worst reviewer of their own design.
- **Don't invent evidence.** If the problem's impact or a metric is unknown, write "unknown" — never fabricate numbers to make the case look stronger.
