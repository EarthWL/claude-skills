# EarthWL Agent Skills

> Opinionated agent skills for Claude Code / Cowork — discipline, not decoration.
> สกิลเอเจนต์แบบมีจุดยืน สำหรับ Claude Code / Cowork — เน้น "วินัยการทำงาน" ไม่ใช่แค่ตกแต่งคำตอบ

**[English](#english) · [ภาษาไทย](#ภาษาไทย)**

These skills encode senior-engineer judgment as enforced steps and refuse-gates, so the right discipline happens automatically. Based on [`thananon/9arm-skills`](https://github.com/thananon/9arm-skills), with skills of our own added on top.

---

## English

### Skills

Each skill is a directory under `skills/<bucket>/<name>/` with a `SKILL.md` (YAML frontmatter — `name`, `description` — plus instructions).

**Engineering**

| Skill | Trigger | What it does | Source |
|---|---|---|---|
| [debug-mantra](./skills/engineering/debug-mantra/SKILL.md) | `/debug-mantra` · auto | Four-step debugging discipline: reproduce → trace the fail path → falsify the hypothesis → cross-reference every breadcrumb. | upstream |
| [post-mortem](./skills/engineering/post-mortem/SKILL.md) | `/post-mortem` | The canonical record of a fixed bug — root cause, fix, validation, how it slipped through. Refuses to draft without a repro, known cause, and validated fix. | upstream |
| [scrutinize](./skills/engineering/scrutinize/SKILL.md) | `/scrutinize` | Outsider-perspective review of a plan/PR/change — questions intent, traces the real code path, verifies the change does what it claims. | upstream |
| [design-doc](./skills/engineering/design-doc/SKILL.md) | `/design-doc` | Google design-doc / RFC discipline — problem → goals/non-goals → alternatives (including doing nothing) → tradeoffs, before any code. | **EarthWL** |
| [rollout](./skills/engineering/rollout/SKILL.md) | `/rollout` | Progressive rollout & blast-radius discipline — staged plan, tested rollback, abort criteria defined up front. Works for code and low-code/workflow changes. | **EarthWL** |
| [tdd](./skills/engineering/tdd/SKILL.md) | auto · `/tdd` | Test-driven development with a red-green-refactor loop and vertical-slice tracer bullets; tests verify behavior through public interfaces, not implementation. | mattpocock |
| [incident-response](./skills/engineering/incident-response/SKILL.md) | auto · `/incident-response` | Live-incident discipline — mitigate first, diagnose later: declare severity, stop the bleeding, assign roles, keep a timeline, then hand off to debug-mantra and post-mortem. | **EarthWL** |
| [adr](./skills/engineering/adr/SKILL.md) | `/adr` | Architecture Decision Record (MADR) — capture one hard-to-reverse decision with context, options + pros/cons, outcome, and consequences; immutable once accepted, superseded rather than edited. | **EarthWL** |
| [performance](./skills/engineering/performance/SKILL.md) | auto · `/performance` | Measure-before-optimize discipline — no speed change without a target, a baseline, and a profile; optimize the biggest contributor, stop at the target, lock in a regression guard. | **EarthWL** |

**Productivity**

| Skill | Trigger | What it does | Source |
|---|---|---|---|
| [kickoff](./skills/productivity/kickoff/SKILL.md) | `/kickoff` | Project kickoff orchestrator — locks scope (goal, definition of done, non-goals) before any work, then routes to the right downstream skill. | **EarthWL** |
| [management-talk](./skills/productivity/management-talk/SKILL.md) | auto | Rewrite engineer-to-engineer content for leadership, shaped for the channel (JIRA, Slack, standup, email, meeting talking-points). | upstream |

The skills chain together: `/kickoff` (scope) → `/design-doc` (how) → `scrutinize` (review) → build → `/rollout` (ship) — and `debug-mantra` → fix → `post-mortem` → `management-talk` when something breaks.

### Layout

Skills live under `skills/`, grouped into buckets: `engineering/` (code work), `productivity/` (non-code workflow tools), `misc/` (rarely used).

### Install

Symlink every shippable skill into `~/.claude/skills/`:

```bash
./scripts/link-skills.sh
```

List every `SKILL.md` in the repo:

```bash
./scripts/list-skills.sh
```

### Credits

Built on [`thananon/9arm-skills`](https://github.com/thananon/9arm-skills) by [@thananon](https://github.com/thananon) (9arm). `debug-mantra`, `post-mortem`, `scrutinize`, and `management-talk` originate upstream. `kickoff`, `design-doc`, and `rollout` are added in this repo. `tdd` is adapted from [`mattpocock/skills`](https://github.com/mattpocock/skills) (MIT © 2026 Matt Pocock). `incident-response` is derived from [Google SRE](https://sre.google/sre-book/managing-incidents/) and [PagerDuty Incident Response](https://response.pagerduty.com/). `adr` follows [Michael Nygard's ADR](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions) and the [MADR](https://adr.github.io/madr/) template. `performance` follows the measure-first tradition and [Brendan Gregg's USE method](https://www.brendangregg.com/usemethod.html).

---

## ภาษาไทย

### สกิล

แต่ละสกิลเป็นโฟลเดอร์ใต้ `skills/<bucket>/<name>/` มีไฟล์ `SKILL.md` (frontmatter `name`, `description` + คำสั่งการทำงาน)

**Engineering (งานวิศวกรรม)**

| สกิล | คำสั่ง | ทำอะไร | ที่มา |
|---|---|---|---|
| [debug-mantra](./skills/engineering/debug-mantra/SKILL.md) | `/debug-mantra` · อัตโนมัติ | วินัยดีบั๊ก 4 ขั้น: reproduce → ไล่ fail path → พยายามล้มสมมติฐาน → โยงทุก breadcrumb เข้าด้วยกัน | upstream |
| [post-mortem](./skills/engineering/post-mortem/SKILL.md) | `/post-mortem` | บันทึกวิศวกรรมของบั๊กที่แก้แล้ว — สาเหตุ, การแก้, validation, ทำไมหลุดมาได้ (ปฏิเสธถ้ายังไม่มี repro/สาเหตุ/ผล validate) | upstream |
| [scrutinize](./skills/engineering/scrutinize/SKILL.md) | `/scrutinize` | รีวิวแบบคนนอก — ตั้งคำถามว่าจำเป็นไหม/มีทางง่ายกว่าไหม แล้วไล่ code path จริงเพื่อยืนยันว่าทำได้ตามที่อ้าง | upstream |
| [design-doc](./skills/engineering/design-doc/SKILL.md) | `/design-doc` | วินัย design-doc/RFC สไตล์ Google — problem → goals/non-goals → ทางเลือก (รวม "ไม่ทำอะไรเลย") → tradeoffs ก่อนลงโค้ด | **EarthWL** |
| [rollout](./skills/engineering/rollout/SKILL.md) | `/rollout` | วินัย progressive rollout — แผนปล่อยทีละขั้น, ทาง rollback, abort criteria กำหนดล่วงหน้า ใช้ได้ทั้งโค้ดและ low-code/workflow | **EarthWL** |
| [tdd](./skills/engineering/tdd/SKILL.md) | อัตโนมัติ · `/tdd` | TDD แบบ red-green-refactor ทีละ vertical slice; เทสต์ตรวจพฤติกรรมผ่าน public interface ไม่ผูกกับ implementation | mattpocock |
| [incident-response](./skills/engineering/incident-response/SKILL.md) | อัตโนมัติ · `/incident-response` | วินัยรับมือเหตุสด — mitigate first, diagnose later: ประกาศ severity, หยุดเลือด, มอบ role, จด timeline แล้วส่งต่อ debug-mantra/post-mortem | **EarthWL** |
| [adr](./skills/engineering/adr/SKILL.md) | `/adr` | Architecture Decision Record (MADR) — บันทึก 1 decision ที่ย้อนยาก: context, ตัวเลือก+pros/cons, outcome, consequences; accepted แล้วห้ามแก้ ใช้ supersede แทน | **EarthWL** |
| [performance](./skills/engineering/performance/SKILL.md) | อัตโนมัติ · `/performance` | วินัย measure-before-optimize — ห้ามแก้เพื่อความเร็วถ้าไม่มี target/baseline/profile; แก้ตัวที่กินมากสุด หยุดที่เป้า ใส่ regression guard | **EarthWL** |

**Productivity (งานเวิร์กโฟลว์)**

| สกิล | คำสั่ง | ทำอะไร | ที่มา |
|---|---|---|---|
| [kickoff](./skills/productivity/kickoff/SKILL.md) | `/kickoff` | ตัว orchestrator เริ่มงาน — ล็อก scope (เป้าหมาย, นิยาม "เสร็จ", non-goals) ก่อนลงมือ แล้ว route ไปสกิลที่เหมาะ | **EarthWL** |
| [management-talk](./skills/productivity/management-talk/SKILL.md) | อัตโนมัติ | แปลงเนื้อหาเชิงเทคนิคให้ผู้บริหารอ่าน ปรับตามช่องทาง (JIRA, Slack, standup, email, talking-points) | upstream |

สกิลออกแบบให้ต่อกันเป็นสายงาน: `/kickoff` (scope) → `/design-doc` (ออกแบบ) → `scrutinize` (รีวิว) → build → `/rollout` (ปล่อย) และเมื่อมีอะไรพัง: `debug-mantra` → แก้ → `post-mortem` → `management-talk`

### โครงสร้าง

สกิลอยู่ใต้ `skills/` แบ่งเป็น bucket: `engineering/` (งานโค้ด), `productivity/` (เครื่องมือเวิร์กโฟลว์ที่ไม่ใช่โค้ด), `misc/` (ใช้นาน ๆ ครั้ง)

### การติดตั้ง

ทำ symlink สกิลทั้งหมดเข้า `~/.claude/skills/`:

```bash
./scripts/link-skills.sh
```

แสดงรายการ `SKILL.md` ทุกไฟล์ใน repo:

```bash
./scripts/list-skills.sh
```

### เครดิต / ต้นทาง

พัฒนาต่อยอดจาก [`thananon/9arm-skills`](https://github.com/thananon/9arm-skills) โดย [@thananon](https://github.com/thananon) (9arm) — `debug-mantra`, `post-mortem`, `scrutinize`, `management-talk` มาจาก upstream ส่วน `kickoff`, `design-doc`, `rollout` เพิ่มเข้ามาใน repo นี้ ส่วน `tdd` ปรับมาจาก [`mattpocock/skills`](https://github.com/mattpocock/skills) (MIT © 2026 Matt Pocock) และ `incident-response` กลั่นจาก Google SRE + PagerDuty Incident Response และ `adr` ยึดแนว Nygard ADR + MADR และ `performance` ยึด measure-first + USE method ของ Brendan Gregg
