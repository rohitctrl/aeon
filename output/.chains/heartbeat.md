⚠️ Heartbeat — 🔴 DEGRADED

## Heartbeat — fleet check (2026-10-01)

**Overall: 🔴 DEGRADED**

🔴 **heartbeat self-check** — last success `2026-09-22 06:50 UTC`, ~9 days ago, against a daily `0 8 * * *` cron. The health canary has not fired in over a week while `digest` and `dental-lead-scout` ran normally today, so the scheduler is live but heartbeat's own daily run is not landing.

🟡 **bd-radar** — enabled in `aeon.yml` (`30 3 * * 1`, Mondays 03:30 UTC) but has **no entry at all** in `memory/cron-state.json`. It has never been dispatched by the scheduler (last Monday window was 2026-09-28).

_No failed, stuck, or degrading skills. No open PRs; GitHub issues are disabled on this repo. Open issues: 0. Status page regenerated → [docs/status.md](/status/)._