# Dispatcher heartbeat

<!-- index: dispatch lane proof-of-life — running, last poll 2026-09-04T13:04:33Z -->

Proof of life for the Jahjah dispatch lane on `germany-vpn`. Rewritten at most once an hour, and
immediately whenever the lane pauses or trips its failure cap.

| | |
|---|---|
| State | **running** |
| Last poll (UTC) | 2026-09-04T13:04:33Z |
| Timer unit | `jahjah-dispatcher.timer` — enabled |
| Jobs run today (UTC 2026-09-04) | 1 of 5 |
| Consecutive failures | 0 of 3 before self-disable |
| Kill switch | clear |

**Reading this file.** The lane polls every 5 minutes. If `Last poll` is more than ~70 minutes old
and the state is not `PAUSED`, the lane is not running — nobody is picking up dispatch issues.
Look for `ALERT-dispatcher-disabled.md` next to this file; if it is absent, the box or the timer
itself is down.
