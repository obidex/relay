# Heartbeat — `jahjah-erp-dispatch`

<!-- index: proof-of-life for the ERP chunk lane — stale > ~70 min means chunks are not being picked up -->

**Written (UTC):** 2026-09-03T21:27:04Z
**State:** running — chunk #97 dispatched
**Chunk in flight:** 97
**Chunks started today (UTC 2026-09-03):** 3 of 3
**Consecutive failures:** 0 of 3 before self-disable
**Kill switch:** clear

Polls `obidex/jahjah-internal` every 2 minutes for an open issue labelled `chunk:approved`.
**Stale by more than ~70 minutes and not `PAUSED` = the lane is not running**, and an approved
chunk will sit untouched. Look for `ALERT-erp-dispatch-disabled.md` next to this file; if it is
absent, the box or the timer itself is down.

Stop: `touch /opt/jahjah/ERP_DISPATCH_OFF` · registry: `docs/runbooks/automations.md`
