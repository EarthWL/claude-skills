---
name: performance
description: Performance-optimization discipline — measure before you optimize. Refuses to change code for speed without a target number, a baseline measurement from a repeatable benchmark, and a profile showing where the time or resources actually go. Optimize the biggest contributor first, one change at a time, re-measuring against the baseline; stop once the target is hit and lock it in with a regression guard. Use proactively when the user wants to make something faster, optimize, reduce latency, cut memory or cost, hit a throughput target, or says something is slow — and on /performance. Do NOT use for a sudden regression that is really a bug (use debug-mantra), a production outage (use incident-response), or premature micro-optimization with no measured problem.
---

# Performance

Measure before you optimize. The bottleneck is almost never where you think it is, and code changed on a hunch usually moves the wrong number. This discipline forces a measurement and a target onto the table before any line is touched for speed.

Built on the "measure first" tradition and Brendan Gregg's USE method.

## When to invoke

- `/performance`
- "make this faster", "optimize this", "reduce latency", "cut memory / cost", "hit N requests/sec", "this is slow"

Like `incident-response`, this one **triggers proactively** on optimization signals — the goal is to catch a "let me just speed this up" before it becomes a guess.

## When NOT to use

- A sudden regression that's really a bug (was fast, now slow) → `debug-mantra`.
- A production outage → `incident-response`.
- Premature micro-optimization with no felt problem and no target — the textbook mistake. Don't.

## Language

Match the user's language. When the user is communicating in Thai, write every user-facing response in **Thai**. Your internal working (reasoning, code, commit messages) may stay in English where natural; code identifiers, file paths, keys, metric names, and profiler/tool output are never translated. Default to the language of the user's most recent message.

## Workflow

### 0. Is this worth optimizing?

Is there a felt problem or a concrete target? If nobody is hurting and there's no number to hit, stop — premature optimization trades real readability for imaginary speed.

### 1. Set the target — a number

"Fast enough" is not a target. Name the metric and the number: p99 latency < 200 ms, throughput > 1000 rps, build < 60 s, memory < 2 GB, cloud bill − 30%. Without a target you can't know when to stop.

### 2. Baseline — measure what you have

Build a repeatable benchmark and record the current number. Make it deterministic (pin inputs, warm caches consistently, control noise). A flaky benchmark lies. This is the feedback loop everything else depends on.

### 3. Profile — find where the time/resource actually goes

Before changing anything, profile. For code: a profiler / flame graph. For resources: the **USE method** — Utilization, Saturation, Errors across CPU, memory, I/O, network. Find the **biggest contributor**, not your guess.

### 4. Optimize the biggest contributor — one change at a time

Amdahl's law: optimizing something that's 5% of runtime caps your gain at 5%. Attack the top of the profile. Make **one** change, then re-measure against the baseline.

### 5. Keep or revert by the number

Did the number move? Keep it. Didn't? Revert it — a change that doesn't move the metric is just risk and noise. Re-profile; the bottleneck has probably moved.

### 6. Stop at the target, then lock it in

When you hit the target, stop — don't gold-plate past the point of felt benefit. Then add a **regression guard** (a benchmark in CI, a perf budget/assertion) so the win can't silently erode, and record the before/after numbers.

## Performance gate — refuse to change code for speed until all of these exist

- [ ] **Target** — a metric and a number.
- [ ] **Baseline** — the current number, from a repeatable benchmark.
- [ ] **Profile** — evidence of where the time/resource actually goes.

If you're editing code to make it faster and can't show the number you're at and the number you're aiming for, stop and measure first.

## Template

```
Target:    <metric + number, e.g. p99 latency < 200 ms>
Baseline:  <current number — how it was measured>
Profile:   <top contributors — where the time/resource goes>
Change:    <one optimization>
Result:    <new number vs baseline>   → keep / revert
Guard:     <benchmark/CI check added so it can't silently regress>
```

## Where it fits

Performance is often a `design-doc` driver (decide the approach with the target in mind) and the subject of an `adr` when the trade-off is significant. Keep `tdd`'s tests green while optimizing — speed that breaks behavior is not a win. Ship perf changes through `rollout` like any risky change; if a perf problem is taking production down, that's `incident-response` first.

## Operating rules

- **Match the user's language** (see Language).
- **Measure, don't guess.** Every speed change is justified by a number, before and after.
- **Biggest contributor first.** Optimizing a 2% hotspot is wasted motion — read the profile.
- **One change at a time.** Batched optimizations make it impossible to know what actually worked.
- **Revert what doesn't move the number.** A neutral "optimization" is pure risk.
- **Stop at the target.** Past the felt benefit, you're trading readability for vanity.
- **Don't sacrifice correctness or clarity** for speed the numbers don't justify.
- **Lock in the win.** No regression guard means the next change quietly takes it back.

---

_Built on the measure-first tradition and Brendan Gregg's [USE method](https://www.brendangregg.com/usemethod.html) / Systems Performance._
