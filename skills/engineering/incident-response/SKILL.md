---
name: incident-response
description: Incident-response discipline for live production problems — mitigate first, diagnose later. Declare a severity, stop the bleeding (roll back, kill switch, fail over) before hunting root cause, assign roles (incident commander, comms, responders), keep a timestamped timeline, communicate on a cadence, then resolve and hand off to debug-mantra for root cause and post-mortem for the record. Use proactively when the user reports a production outage, customer-facing breakage, data loss, a security incident, says something is down, users cannot access something, or declares a sev/severity — and on /incident-response. Do NOT use for ordinary bugs with no live impact (use debug-mantra), a planned rollback (use rollout), or the post-incident writeup (use post-mortem).
---

# Incident Response

Something is broken in production right now. The job is not to understand it — the job is to **make the pain stop**, then understand it. Mitigate first, diagnose later.

Derived from Google SRE incident management and the PagerDuty Incident Response guide.

## When to invoke

- `/incident-response`
- The user reports a live problem — "production is down", "customers can't log in", "we're losing data", "the site is 500ing", "security breach", "sev1".

Unlike the other skills here, this one **triggers proactively** on clear incident signals — during an outage you don't want to wait for a command.

## When NOT to use

- An ordinary bug with no live customer impact → `debug-mantra`.
- A planned, controlled rollback during a release → `rollout`.
- The post-incident writeup → `post-mortem`.

If it's unclear whether this is a real incident or just a bug, ask one question — "is this affecting production / users right now?" — and route accordingly.

## Language

Match the user's language. When the user is communicating in Thai, write every user-facing response in **Thai**. Your internal working (reasoning, code, commit messages) may stay in English where natural; code identifiers, file paths, and keys are never translated. Status updates and the timeline follow the user's language, but customer-facing comms follow the audience you are writing for; if there is nothing to infer a language from, ask first. Default to the language of the user's most recent message.

## Workflow

### 0. Declare severity — it sets the whole response

| Sev | Impact | Response |
|---|---|---|
| **SEV1** | Widespread outage, data loss, or security breach | All hands. IC + comms. Updates ~every 30 min. |
| **SEV2** | Significant degradation, a subset of users, workaround exists | IC. Updates ~hourly. |
| **SEV3** | Minor or internal, limited impact | Single responder, lightweight. |

State the severity out loud and why. When unsure between two, pick the higher one — you can downgrade later.

### 1. Stabilize — stop the bleeding

The first action reduces impact **now**, even if it's ugly and temporary:

- Roll back the most recent change / deploy (the usual cause). Hand to `rollout`'s rollback path if one exists.
- Flip the kill switch / disable the feature flag.
- Fail over, scale up, shed load, or put up a maintenance page.

Reach for the fastest mitigation that reduces impact. **Do not start root-causing while a fast mitigation is available and customers are hurting.** This is the gate.

### 2. Assign roles

For anything past a trivial SEV3, name who is doing what (one person can wear several hats, but say so):

- **Incident Commander (IC)** — coordinates, decides, owns the incident. Not hands-on-keyboard.
- **Comms** — updates stakeholders and customers.
- **Responders** — hands on keyboard, one change at a time.

### 3. Communicate on a cadence

- One channel, one source of truth.
- Post updates on the severity's cadence **even when there's no change** ("still investigating, next update in 30 min"). Don't go dark.
- Separate internal updates from customer-facing ones; say only what's known.

### 4. Keep a timeline

Log every observation and action with a timestamp as you go: what you saw, what you changed, what happened. This is the raw material for the `post-mortem` and stops responders from repeating each other's work.

### 5. Diagnose — only after stable, or in parallel by someone else

Once impact is mitigated, hand the root-cause hunt to `debug-mantra`. In a larger incident a separate responder can diagnose in parallel — but diagnosis must never block mitigation.

### 6. Resolve and stand down

- Verify the impact is actually gone against the **original** symptom — not a nearby proxy.
- Declare the incident resolved and send the all-clear on the same channel.
- Downgrade severity as impact drops.

### 7. Hand off

Every incident that mattered gets a blameless `post-mortem` — pass it the timeline. If leadership needs an update, route the summary through `management-talk`.

## Incident-response gate — before you start digging into root cause

- [ ] **Severity declared.**
- [ ] **Mitigation attempted** — the fastest available action to reduce impact is in flight or done.
- [ ] **Timeline started.**

If impact is ongoing and you find yourself reading code instead of mitigating, stop and mitigate first.

## Templates

```
STATUS UPDATE — SEV<n> — <time>
Impact:   <who/what is affected, in user terms>
Current:  <what we're doing right now>
Next update: <time>
```

```
TIMELINE
<time> — <observation or action> — <result>
```

## Where it fits

`rollout` (ship) → if it breaks in production → **`incident-response`** (stabilize) → `debug-mantra` (root cause) → `post-mortem` (record) → `management-talk` (report up).

## Operating rules

- **Mitigate first, diagnose later.** Restoring service beats understanding it. Root cause can wait; customers can't.
- **Roll back first.** The most recent change is the prime suspect — revert it before theorising.
- **Blameless.** Describe what happened and what was done, never who to blame. That's how the timeline stays honest.
- **No solo irreversible moves under pressure.** Destructive or one-way actions need IC sign-off — panic plus irreversible is how one incident becomes two.
- **Never go dark.** A "no change yet" update on cadence beats silence.
- **Severity sets the intensity.** Don't run a SEV3 like a SEV1 or vice versa.
- **One change at a time.** Simultaneous fixes make it impossible to know what worked.

---

_Derived from [Google SRE — Managing Incidents](https://sre.google/sre-book/managing-incidents/) and the [PagerDuty Incident Response](https://response.pagerduty.com/) guide._
