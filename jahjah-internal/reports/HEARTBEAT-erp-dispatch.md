# Heartbeat — `jahjah-erp-dispatch`

<!-- index: proof-of-life for the ERP chunk lane — stale > ~70 min means chunks are not being picked up -->

**Written (UTC):** 2026-09-12T17:01:04Z
**State:** OK — idle, no `chunk:approved` issue open
**Chunk in flight:** none
**Chunks started today (UTC 2026-09-12):** 2 of 3
**Consecutive poll failures:** 0 of 3 before self-disable
**Chunk failures:** 0 of 3 before self-disable
**Usage cap:** none
**Dispatches in the last 24 h with a chunk window open at the approval instant:** 0
**Kill switch:** clear

Polls `obidex/jahjah-internal` every 2 minutes for an open issue labelled `chunk:approved`.
**Stale by more than ~70 minutes and not `PAUSED` = the lane is not running**, and an approved
chunk will sit untouched. Look for `ALERT-erp-dispatch-disabled.md` or
`ALERT-erp-dispatch-chunks-disabled.md` next to this file; if both are absent, the box or the timer
itself is down.

The last line is a **detective** count, not an alarm (`D237`). An approval that lands while a
chunk is running is exactly what the owner does when he approves the next chunk from a phone; it
is also what a chunk approving its own successor looks like. The lane records which it saw and
refuses nothing.

Stop: `touch /opt/jahjah/ERP_DISPATCH_OFF` · registry: `docs/runbooks/automations.md`
