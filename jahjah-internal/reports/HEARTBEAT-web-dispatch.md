# Heartbeat — `jahjah-web-dispatch`

<!-- index: proof-of-life for the website chunk lane — stale > ~70 min means chunks are not being picked up -->

**Written (UTC):** 2026-09-06T19:28:04Z
**State:** OK — idle, no `chunk:approved` issue open
**Chunk in flight:** none
**Chunks started today (UTC 2026-09-06):** 0 of 3
**Consecutive failures:** 0 of 3 before self-disable
**Kill switch:** clear

Polls `obidex/jahjah-website` every 2 minutes for an open issue labelled `chunk:approved`.
**Stale by more than ~70 minutes and not `PAUSED` = the lane is not running**, and an approved
chunk will sit untouched. Look for `ALERT-web-dispatch-disabled.md` next to this file; if it is
absent, the box or the timer itself is down.

Stop: `touch /opt/jahjah/WEB_DISPATCH_OFF` · registry: `docs/runbooks/automations.md`
