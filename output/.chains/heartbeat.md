🚨 🔴 digest — root cause found (4-day outage)

## 🔴 digest — root cause found (4-day outage)

`digest` has **failed every day since 2026-10-07**, and the previous heartbeats had the diagnosis wrong: it is *not* a hang. The dispatch itself never starts a job.

**Verified**
- Runs 10-07, 10-08, 10-09, 10-10 all conclude `failure` with **zero jobs** — GitHub reports "failed because of a workflow file issue". The skill never runs.
- `output/digest/` has produced nothing since `2026-10-06.md`.

**Root cause — the lengthened `var`**
- The digest `var` in `aeon.yml` was expanded 2026-10-06 (entry-level-only + ₹6 LPA floor).
- Run-name went **94 → 467 chars**.
- Dispatch history is a clean split: short var → **6/6 success** (10-01…10-06); long var → **0/4** (10-07…10-10). Nothing else changed.

**Why it stays silent**
The run dies before any step executes, so the scheduler only ever records `dispatched` — the state file reads "stuck", and the failure circuit-breaker (keys on `consecutive_failures`) never trips. It will keep failing quietly every day.

**Fix (one change)**
Shorten the digest `var` back to the topic only — `data analyst and BI job market India - fresh roles, stack shifts, comp reality` — and move the entry-level / ₹6 LPA policy text into `skills/digest/SKILL.md` (or a `memory/topics/` note the digest reads), so the run-name stays short. That restores the exact config that ran green through 10-06.

Everything else is healthy: dental-lead-scout 12/12, heartbeat 10/10, bd-radar 1/1.

<sub>Marked *unverified*: the precise GitHub limit that rejects the longer run-name (the docs state none). What's verified is the failure and its perfect correlation with the var change.</sub>