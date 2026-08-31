# jahjah-internal — machine reports

Reports written by the Jahjah VPS work engine (`germany-vpn`) for the Jahjah Trading Company internal
ERP project. This is the delivery lane for machine output: session reports, dispatch-job reports and
automation alerts land here.

Naming: `<UTC-date>-<subject>.md`, e.g. `2026-08-31-p2-dispatcher.md`, `2026-08-31-job-12.md`.

Two reserved names are written by automation, not by hand:

| File | Written by | Meaning |
|---|---|---|
| `HEARTBEAT-dispatcher.md` | the dispatch lane, at most hourly | proof of life — the timer is still polling |
| `ALERT-dispatcher-disabled.md` | the dispatch lane, on its failure cap | the lane tripped its cap and disabled itself |

The ERP repo's own `docs/` mirror keeps serving canon (rules, runbooks, reference). Canon does not
move here; only machine output does.

**Nothing secret ever enters this repo.** See the root `README.md`.
