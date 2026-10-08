🚨 Heartbeat — fleet degraded (digest stuck 2 days)

🔴 STUCK: **digest** — dispatched 2026-10-08 02:00 UTC and never reported back (~7h). Second consecutive missed daily run: the 10-07 **and** 10-08 dispatches both hung, and there is no `output/digest/*.md` since 10-06.

🟡 STALE: digest's last success is 2026-10-06 08:09 UTC — now **>2× its daily interval**, so the primary job-market pipeline has produced nothing for two days.

Everything else green: dental-lead-scout 10/10, bd-radar 1/1, heartbeat 8/8. No open PRs; issues disabled.

Next step: inspect the digest workflow — two dispatches in a row hung before writing any output.