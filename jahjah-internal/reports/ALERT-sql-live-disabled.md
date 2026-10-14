# ALERT — `jahjah-sql-live` disabled itself

<!-- index: ALERT — the sql-live job hit its failure cap and turned itself off -->

**When (UTC):** 2026-10-14T03:15:07Z
**Box:** `germany-vpn`
**Unit:** `jahjah-sql-live.timer` — `systemctl disable --now` has been run on it. **It will not come back on
its own, and it will not come back after a reboot.**

## Why

The job failed **3 times in a row** (cap is 3).

Last failure: 1 of 88 suite(s) did not pass against live: po_schedule_one_transaction_tests

## What is no longer happening

See the `jahjah-sql-live` row in `docs/runbooks/automations.md` for what this job does. Until
someone re-enables it, it is not being done at all.

## How to look into it

    tail -50 /opt/jahjah/sql-live.log
    systemctl status jahjah-sql-live.service
    journalctl -u jahjah-sql-live.service -n 100

## How to bring it back

Fix the cause first, then:

    rm -f /opt/jahjah/sql-live/state/consecutive-failures
    systemctl enable --now jahjah-sql-live.timer
