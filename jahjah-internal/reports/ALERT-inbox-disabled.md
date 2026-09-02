# ALERT — `jahjah-inbox` disabled itself

<!-- index: ALERT — the inbox job hit its failure cap and turned itself off -->

**When (UTC):** 2026-09-02T16:26:34Z
**Box:** `germany-vpn`
**Unit:** `jahjah-inbox.timer` — `systemctl disable --now` has been run on it. **It will not come back on
its own, and it will not come back after a reboot.**

## Why

The job failed **3 times in a row** (cap is 3).

Last failure: CHUNK-4.md rejected: no `MODEL: <model id>` line

## What is no longer happening

See the `jahjah-inbox` row in `docs/runbooks/automations.md` for what this job does. Until
someone re-enables it, it is not being done at all.

## How to look into it

    tail -50 /opt/jahjah/inbox.log
    systemctl status jahjah-inbox.service
    journalctl -u jahjah-inbox.service -n 100

## How to bring it back

Fix the cause first, then:

    rm -f /opt/jahjah/inbox/state/consecutive-failures
    systemctl enable --now jahjah-inbox.timer
