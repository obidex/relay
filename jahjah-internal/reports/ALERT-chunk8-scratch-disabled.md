# ALERT — `jahjah-chunk8-scratch` disabled itself

<!-- index: ALERT — the chunk8-scratch job hit its failure cap and turned itself off -->

**When (UTC):** 2026-09-10T07:33:26Z
**Box:** `germany-vpn`
**Unit:** `jahjah-chunk8-scratch.timer` — `systemctl disable --now` has been run on it. **It will not come back on
its own, and it will not come back after a reboot.**

## Why

The job failed **4 times in a row** (cap is 3).

Last failure: gh issue list failed on obidex/chunk8-nonexistent (see the lines above in this log)

## What is no longer happening

See the `jahjah-chunk8-scratch` row in `docs/runbooks/automations.md` for what this job does. Until
someone re-enables it, it is not being done at all.

## How to look into it

    tail -50 /opt/jahjah/chunk8-scratch.log
    systemctl status jahjah-chunk8-scratch.service
    journalctl -u jahjah-chunk8-scratch.service -n 100

## How to bring it back

Fix the cause first, then:

    rm -f /opt/jahjah/chunk8-scratch/state/consecutive-failures
    systemctl enable --now jahjah-chunk8-scratch.timer
