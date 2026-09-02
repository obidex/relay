# Inbox lane heartbeat

<!-- index: inbox lane proof-of-life — running, last poll 2026-09-02T16:12:53Z -->

Proof of life for the inbox lane on `germany-vpn` — the lane that starts a chunk from a file the
owner pushes to `jahjah-internal/inbox/`. Rewritten at most once an hour, and immediately whenever the lane
pauses or trips its failure cap.

| | |
|---|---|
| State | **running** |
| Last poll (UTC) | 2026-09-02T16:12:53Z |
| Timer unit | `jahjah-inbox.timer` — enabled |
| Consecutive failures | 0 of 3 before self-disable |
| Kill switch | clear |
| Self-disabled | no |
| Dry run | YES — validates and reports, never starts |
| Chunk in flight | none (dry run: **would start CHUNK-1**, model `claude-opus-5`) |

**How to put a chunk in.** Push `jahjah-internal/inbox/CHUNK-<n>.md` to this repository's `main`. Line 1 must be
`SHA256: <sha256 of everything after line 1>`; the body must contain a `SESSION: jahjah` line and a
`MODEL: <id>` line; and the commit must be **pushed by the owner directly** — both author and
committer must be `obidex <144545793+obidex@users.noreply.github.com>`. **A commit made through the GitHub web UI has
`GitHub <noreply@github.com>` as its committer and WILL be refused.**

**Reading this file.** The lane polls every 5 minutes. If `Last poll` is more than ~70 minutes old
and the state is not `PAUSED`, the lane is not running. Look for `ALERT-inbox.md` and
`ALERT-inbox-disabled.md` next to this file.
