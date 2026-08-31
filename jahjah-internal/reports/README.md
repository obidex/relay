# jahjah-internal — machine reports

Reports written by the Jahjah VPS work engine (`germany-vpn`) for the Jahjah Trading Company internal
ERP project. This is the delivery lane for machine output: session reports, dispatch-job reports,
scheduled-job reports and automation alerts land here.

## Start at the index

**`INDEX.md`** lists every file in this folder with the time it was last written and a one-line
summary. Every publisher rebuilds it, so it is never stale. Read it instead of listing this directory
through the GitHub contents API, which is rate-limited.

## Two kinds of file

**Standing files are overwritten in place.** They always hold the CURRENT answer, and never
accumulate — **git history is their archive**, so `git log -p <file>` is every previous edition.

| File | Written by | Meaning |
|---|---|---|
| `INDEX.md` | every publisher | what is in this folder, when, and what it says |
| `HEALTH-daily.md` | `jahjah-health`, 05:00 UTC daily | the liveness ledger: the box, and every automation on it |
| `SCAN-trivy.md` | `jahjah-scan-trivy`, Mondays 03:00 UTC | known vulnerabilities in dependencies and container images |
| `SCAN-gitleaks.md` | `jahjah-scan-gitleaks`, Mondays 04:00 UTC | committed-secret scan of both repositories' full history |
| `HEARTBEAT-dispatcher.md` | the dispatch lane, at most hourly | proof of life — the dispatch timer is still polling |
| `ALERT-<job>-disabled.md` | any job, on its failure cap | that job failed 3 times running and switched itself off |
| `ALERT-gitleaks-<date>.md` | `jahjah-scan-gitleaks`, on any hit | a secret was found in git history — treat as critical |

**Dated files accumulate, and are pruned.** Named `<UTC-date>-<subject>.md`, e.g.
`2026-08-31-p2-dispatcher.md`, `2026-08-31-job-12.md`. `jahjah-retention` keeps the newest ten of
these per folder every Sunday and deletes the rest — **from the listing only**; they remain in git
history. Standing files and every `ALERT-*` are exempt from pruning.

## Which files prove the machine is alive

`HEALTH-daily.md` is the one to check. If its timestamp is more than ~26 hours old, the health job
itself has stopped. `HEARTBEAT-dispatcher.md` covers the dispatch lane on a much shorter cycle: more
than ~70 minutes old and not marked `PAUSED` means that lane is not running.

The ERP repo's own `docs/` mirror keeps serving canon (rules, runbooks, reference). Canon does not
move here; only machine output does.

**Nothing secret ever enters this repo.** See the root `README.md`. The scanners above are built to
this rule specifically: a secret-scanner report names the rule, file, line and commit, and never the
secret itself, because publishing it here would be the leak.
