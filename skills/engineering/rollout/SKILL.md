---
name: rollout
description: Progressive rollout and blast-radius discipline — before shipping a risky change, force answers to three questions — how to expose it in stages, how to detect it going wrong, and how to roll it back fast. Works for software deploys (canary, feature flags, staged percentages, kill switch) and for low-code or workflow changes (pilot group, previous-version revert on Kissflow or Nocoly). Refuses to start the rollout until the blast radius, a tested rollback path, and abort criteria are defined, then plans staged exposure with detection at each step. Invoke ONLY when the user explicitly runs /rollout or asks how to roll out, ship, release, or deploy a change safely. Do NOT auto-trigger on ordinary requests, quick questions, casual conversation, or local development work.
---

# Rollout

A change isn't done when it's built — it's done when it's safely in front of everyone who needs it. The riskiest moment is when the change first meets reality. Ship it in stages, watch it, and be ready to undo it.

Three questions you must be able to answer **before** shipping:

1. How do we expose this gradually, not to everyone at once?
2. How will we know it's going wrong, and how fast?
3. How do we roll it back?

## When to invoke

- `/rollout`
- "how do we roll this out / ship / release / deploy this safely"
- "rollout plan for ..." / "we're about to push X to everyone"

This skill is **manual** — invoked deliberately, not automatically.

## When NOT to use

- A trivial, low-risk change with no real blast radius (internal note, typo fix).
- Local or dev work that isn't going in front of users yet.
- An active incident where the rollback *is* the response — handle the incident first (reversibility still matters, so flag it).

## Communication language

Settle this before planning — it governs every word this skill says back (questions, the plan, hand-off notes):

- **Mirror the user.** Reply in the same language the user wrote their request in. If they switch languages mid-conversation, switch with them.
- **No text to go on? Ask first.** If `/rollout` is invoked on its own with nothing else to infer a language from, ask which language to use **before** planning, then proceed in that language.
- Skill names and keywords (`debug-mantra`, `/rollout`, etc.) stay as-is regardless of language.

## Workflow

Run in order. Do not start exposing the change until the gate is satisfied.

### 0. Calibrate by blast radius — not by effort

Size the rigor by *who or what gets hurt if this fails*, not by how hard it was to build. A one-line auth change can be high-risk; a large internal-only refactor can be low.

| Blast radius | Looks like | Rollout rigor |
|---|---|---|
| **Low** | Internal-only, few users, trivially reversible | Light: ship, watch, keep an undo path. |
| **Medium** | Customer-facing, one workflow/service, recoverable | Staged rollout + defined detection + rollback. |
| **High** | Data migration, money, auth, org-wide workflow, hard to reverse | Full: staged exposure + metrics + abort criteria + tested rollback + comms. |

### 1. Name the blast radius

In one line: what breaks, and who feels it, in the worst case? If you can't name it, you can't size the rollout — find out first.

### 2. Make it reversible — before you ship, not after

- **Software:** can you turn it off without a full redeploy? Feature flag, kill switch, config toggle.
- **Low-code / workflow:** keep the previous workflow version; know exactly how to revert or re-publish it; do not delete the old one until the new one is proven.
- **Data:** is the change backward-compatible? Prefer expand-then-contract; take a backup before anything destructive.
- If it is **not** reversible, say so loudly — that raises the bar for every other step.

### 3. Plan the stages

- **Software:** canary → 1% → 10% → 50% → 100%, with a soak/bake time at each step.
- **Low-code / workflow:** test workspace → pilot group (one team) → department → whole org.
- For each stage: how long to wait before advancing, and who is watching.

### 4. Define detection + abort criteria — before stage 1

- **Detection signal:** what tells you it's working vs. going wrong? Error rate, latency, support tickets, failed submissions/approvals, user complaints.
- **Abort criteria:** the pre-agreed threshold that triggers a rollback — decided **now**, so nobody debates it mid-incident.
- **Who watches**, on what dashboard, for how long.

### 5. Go / no-go gate — refuse to start the rollout until all four are answered

- [ ] **Blast radius** named.
- [ ] **Reversible** — rollback / kill switch / previous version ready (or explicitly flagged irreversible, with extra safeguards).
- [ ] **Staged plan** with wait times.
- [ ] **Detection signal + abort criteria** defined before stage 1.

If any is missing, list what's missing and stop.

### 6. Roll out, watch, record

- Advance one stage at a time. At each stage, check the signal against the abort criteria **before** widening.
- If abort criteria are hit → **roll back first, diagnose after.** Stop the bleeding, then hand the diagnosis to `debug-mantra`.
- Note what happened at each stage as you go.

### 7. Close out

- Full rollout reached and stable through the bake period → done.
- Remove the flag / retire the old version once safely at 100% — don't leave dead toggles or stale workflow versions lying around.

## Plan template

```
Change:        <what is shipping>
Blast radius:  <who/what is hurt if it fails, worst case>
Reversible?:   Y/N — <flag / kill switch / previous version / backup>
Stages:        <stage → audience or % → bake time → who watches>
Detection:     <signal = working  |  signal = going wrong>
Abort criteria: <threshold that triggers rollback>
Rollback:      <exact steps, who runs them, how fast>
Comms:         <who to tell, when>  (if customer-facing)
```

## Where it fits

Build → `scrutinize` (review) → `rollout` (ship safely). If something goes wrong during rollout, roll back, then hand the diagnosis to `debug-mantra`. This is the safe-landing end of the `/kickoff` → `/design-doc` → build chain.

## Operating rules

- **Match the user's language.** Communicate in whatever language the user wrote in; if `/rollout` arrives with no text to infer from, ask which language first and wait.
- **Size by blast radius, not effort.** The amount of risk decides the rigor — not how long the change took to build.
- **Reversibility is non-negotiable.** If you can't undo it fast, fixing that is step one, not an afterthought.
- **Decide abort criteria before stage 1.** Mid-incident is the worst possible time to invent a threshold.
- **Roll back first, diagnose later.** Restore safety, then investigate. The write-up comes after.
- **One stage at a time.** Don't skip stages to save time — the bake period *is* the safety.
- **Clean up after.** Remove dead flags and retire old versions once the change is stable.
- **Don't manufacture rigor for trivial changes.** Low blast radius gets the light path; over-process is its own failure.
