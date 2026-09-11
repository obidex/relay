KIND: final
The owner decided CI gets no new Supabase account token. PR #153 now has CI reach the dev database with the database password over the IPv4 pooler, from two small jobs that install nothing, and the old token secret is deleted. That unblocked the cards work: #136 merged with three new workflow rules (preflight before work, CI reds reported as INFRA or TEST, and self-unblock only for Tier-1/2). Along the way I fixed a CI bug where editing a merged PR cancelled main's post-merge run. PR #154 retired the unused dispatcher lane. Every merge's post-merge main run is green and Vercel is READY; the box's lane code matches main.

=== RELAY ===
HEAD: 5909516 | tree: clean
CI: pass — post-merge main runs green for 30e43f8 (#153, attempt 2 after a concurrency cancel), c7f63d9 (#136) and 5909516 (#154)
DONE: #153 e2e over the IPv4 pooler, no PAT in CI, SUPABASE_ACCESS_TOKEN secret deleted (D245) · #136 cards/auto-chain/close-out/canon-lint/generated ledger + three workflow rules + concurrency fix (D244, D246) · #154 jahjah-dispatcher retired (D247) · #97 and #102 closed · Vercel READY on all three
FILES: #153 7 files (.github/workflows/ci.yml, scripts/e2e-purge.sh, e2e/global-teardown.ts, e2e/cleanup-fixtures.sql, canon) · #136 21 files · #154 13 files
FINDINGS/BLOCKERS: none open — pooler host is aws-1 (the plan's aws-0 answers "tenant not found") · LOW residuals registered: pooler TLS cert not verified (SCRAM enforced), card-lane items under D244
NEXT-NEEDED: none
=== END ===
LESSON: editing a merged PR's body fires a pull_request run whose github.ref is main — a ref-only concurrency group let it cancel main's post-merge run.
