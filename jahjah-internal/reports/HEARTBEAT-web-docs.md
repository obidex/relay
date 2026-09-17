# Heartbeat — `jahjah-web-docs`

<!-- index: proof-of-life for the website canon mirror — stale > ~70 min means the mirror is not running -->

**Written (UTC):** 2026-09-17T11:07:04Z
**State:** OK — mirrored 485e250
**Mirrored `master`:** `485e2507e8b39109648b5d98713bfa553e0e5d1f`
**Runs:** every 30 minutes. **Stale by more than ~70 minutes = this job is not running**, and
`jahjah-website/docs/` is then older than it looks.

The mirrored documents themselves are at
`https://raw.githubusercontent.com/obidex/relay/main/jahjah-website/docs/INDEX.md`.
That file changes only when `master` changes; **this** file is what says the job is alive.

Stop: `touch /opt/jahjah/WEB_DOCS_OFF` · registry: `docs/runbooks/automations.md`
