---
name: adr
description: Architecture Decision Record discipline in the MADR style — capture a single significant, hard-to-reverse decision as a short immutable record covering the context and problem, the options considered with their pros and cons, the chosen outcome and why, and the consequences. Refuses to write a record without at least two real options and named consequences; one decision per ADR; accepted records are never edited, only superseded by a new one. Saves to docs/adr/NNNN-title.md with a status lifecycle (proposed, accepted, superseded). Invoke ONLY when the user explicitly runs /adr or asks to record, log, or document an architecture or technical decision. Do NOT auto-trigger on ordinary requests, reversible choices, or a full design (use design-doc for that).
---

# ADR — Architecture Decision Record

Capture **one** significant decision so the next person (and future-you) can recover *why* it was made — not just what the code does. The value isn't the document; it's forcing the alternatives and the trade-off onto the page before the reasoning evaporates.

MADR style: context → options with pros and cons → outcome → consequences.

## When to invoke

- `/adr`
- "record / log / document this decision", "write an ADR", "capture why we chose X"

This skill is **manual** — invoked deliberately, not automatically.

## When NOT to use

- A reversible or trivial choice — just make it, no ceremony.
- The whole design — that's `design-doc`. (A design-doc often *spawns* several ADRs, one per hard decision.)
- Recording a fixed bug — that's `post-mortem`.

## Language

Match the user's language. When the user is communicating in Thai, write every user-facing response in **Thai**, and write the ADR in Thai too — unless the user says the repo keeps its records in another language. Your internal working may stay in English; code identifiers, file paths, and keys are never translated. The status keywords (proposed, accepted, superseded) stay as-is. Default to the language of the user's most recent message.

## Workflow

### 0. Is this ADR-worthy?

Record it only if the decision is **significant and hard to reverse** — it shapes structure, sets a precedent, or future-you will ask "why on earth did we do it this way." If it's cheap to change later, skip the ceremony.

### 1. Frame the context and problem

Two to four sentences: what forces this decision, and why now? State the drivers/constraints that a good option must satisfy.

### 2. List the options considered — at least two, real

Give each option a fair sketch with its pros and cons. "Do nothing / keep the status quo" is a valid option. Strawmen you only set up to knock down don't count — if there was genuinely only one option, this isn't a decision worth an ADR.

### 3. Decide and name the consequences

State the chosen option and justify it against the drivers. Then name the consequences — the good **and** the trade-off you are accepting. Every decision costs something; if you can't name the cost, you haven't understood the decision.

### 4. Status and save

Save to `docs/adr/NNNN-kebab-title.md` with the next sequential number. New ADRs start `proposed`; mark `accepted` once agreed.

## ADR gate — refuse to write the record until all of these exist

- [ ] **One decision**, framed as a problem with context (why now).
- [ ] **At least two real options** considered.
- [ ] **Chosen outcome** with a justification tied to the drivers.
- [ ] **Consequences** named — the upside and the accepted trade-off.

If any is missing, say what's missing and stop.

## Template (MADR)

```
# NNNN. <short decision title>

- Status: proposed | accepted | superseded by NNNN
- Date: YYYY-MM-DD
- Deciders: <who>

## Context and Problem Statement
<what forces this decision, why now>

## Decision Drivers
- <constraint / quality the choice must satisfy>

## Considered Options
1. <option A>
2. <option B>
3. <option C / do nothing>

## Decision Outcome
Chosen: <option>, because <justification tied to the drivers>.

### Consequences
- Good — <what improves>
- Trade-off — <what we accept / give up>

## Pros and Cons of the Options
### <option A>
- Good — ...
- Bad — ...
### <option B>
- Good — ...
- Bad — ...

## More Information
<links: design-doc, PR, related or superseded ADRs>
```

## Status lifecycle and superseding

`proposed` → `accepted`. An accepted ADR is **immutable** — you don't rewrite history. To change a decision, write a **new** ADR that supersedes the old one: set the new one's context to reference the old, and mark the old one `superseded by NNNN` with a link. Both records stay. The chain of superseded ADRs *is* the decision history.

## Where it fits

`design-doc` explores the whole design → `/adr` locks each significant decision that came out of it. `/kickoff` can route here when a decision needs recording. Other skills (`tdd`, `scrutinize`, `improve-codebase-architecture`) read the ADRs in the area they touch.

## Operating rules

- **Match the user's language** (see Language).
- **One decision per ADR.** If you're recording several, write several.
- **At least two real options.** A single-option ADR is just a note — don't dress it up.
- **Name the trade-off.** A decision with only upsides is a decision you haven't understood.
- **Record the decision, not the implementation.** The code shows how; the ADR shows why.
- **Immutable once accepted.** Revisit by superseding, never by editing. Keep the history honest.
- **Number sequentially.** Never reuse or renumber.

---

_Based on Michael Nygard's [Documenting Architecture Decisions](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions) and the [MADR](https://adr.github.io/madr/) template._
