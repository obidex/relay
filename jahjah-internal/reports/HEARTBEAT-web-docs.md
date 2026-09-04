# Heartbeat — `jahjah-web-docs`

<!-- index: proof-of-life for the website canon mirror — stale > ~70 min means the mirror is not running -->

**Written (UTC):** 2026-09-04T11:37:04Z
**State:** OK — master unchanged
**Mirrored `master`:** `7afc84a8471bb2090214a1e953fadca5cc5e8988`
**Runs:** every 30 minutes. **Stale by more than ~70 minutes = this job is not running**, and
`jahjah-website/docs/` is then older than it looks.

The mirrored documents themselves are at
`https://raw.githubusercontent.com/obidex/relay/main/jahjah-website/docs/INDEX.md`.
That file changes only when `master` changes; **this** file is what says the job is alive.

Stop: `touch /opt/jahjah/WEB_DOCS_OFF` · registry: `docs/runbooks/automations.md`
