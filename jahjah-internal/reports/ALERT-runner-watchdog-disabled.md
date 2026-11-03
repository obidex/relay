# ALERT — `jahjah-runner-watchdog` disabled itself

<!-- index: ALERT — the runner-watchdog job hit its failure cap and turned itself off -->

**When (UTC):** 2026-11-03T17:40:02Z
**Box:** `germany-vpn`
**Unit:** `jahjah-runner-watchdog.timer` — `systemctl disable --now` has been run on it. **It will not come back on
its own, and it will not come back after a reboot.**

## Why

The job failed **2 times in a row** (cap is 2).

Last failure: gha-runner-gha-quick still takes no jobs after its restart (job 126067916806 of obidex/jahjah-internal queued 48 min)

## What is no longer happening

See the `jahjah-runner-watchdog` row in `docs/runbooks/automations.md` for what this job does. Until
someone re-enables it, it is not being done at all.

## How to look into it

    tail -50 /opt/jahjah/runner-watchdog.log
    systemctl status jahjah-runner-watchdog.service
    journalctl -u jahjah-runner-watchdog.service -n 100

## How to bring it back

Fix the cause first, then:

    rm -f /opt/jahjah/runner-watchdog/state/consecutive-failures
    systemctl enable --now jahjah-runner-watchdog.timer
