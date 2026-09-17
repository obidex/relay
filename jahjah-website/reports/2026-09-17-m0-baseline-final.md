M0-baseline (#113) is done. It built one reusable script (`scripts/dispatch/metrics.mjs`) that measures a chunk's GitHub issue and a local Claude Code transcript, ran it against the last two real chunks (#97, #104), and posted the resulting numbers as a table on the issue — the baseline the v3 dispatcher must beat. No site code changed. My own reviewer subagent found and I fixed a real issue twice: a transcript-summing bug that could leak arbitrary non-numeric text to stdout, and a wrong commit hash in a canon ledger row. Codex hit its usage-limit response on both PRs (no verdict either time); recorded, not treated as approval.

=== REPORT: m0-baseline · done ===
HEAD: a884e8e | tree: clean | branch: master
PRs: #115 dd06703 merged (T1: scripts/dispatch/metrics.mjs) · #116 a884e8e merged (T3: STATE canon)
CI: master run 35171895439 in progress (previous run on dd06703 green); PROD: READY | live probes: 2/2 (`/`, `/ar/`)
DONE:
- preflight: HEAD == origin/master `96aa8a3`; 111 tracked files; `npm ci` and build exit 0; required env names present via a working Sanity-backed build; STATE + reference read, no contradicting assumptions
- T0: labels moved `chunk:proposed` -> `chunk:running`
- T1: `scripts/dispatch/metrics.mjs` (issue + transcript modes); reviewer pass found 2 BLOCK + 9 FIX (transcript could leak non-numeric/attacker text to stdout, missing reference regen, `gh` calls missing `-R`/`--limit`/`timeout`, unguarded `JSON.parse`, unvalidated `<n>`, missing cache-token fields, whole-file read instead of streaming) — all fixed and re-verified against the reviewer's own PoCs; PR #115 merged on green CI (Codex: usage-limit response, no verdict)
- T2: ran `issue 97`/`issue 104` and `transcript` on the two matching local sessions (identified by content timestamp — see DEVIATIONS); posted the metrics table comment on #113 with owner-action counts supplied by the plan
- T3: STATE ledger row for M0-baseline added, retired P2b-1c row moved to the archive, `master` HEAD line updated; PR #116's canon-numbers reviewer pass caught one wrong commit hash (P3-B1's close commit), fixed and re-pushed; merged on green CI (Codex: usage-limit response again, no verdict)
DEVIATIONS: the plan's mtime-window instruction for picking transcript files didn't hold on disk — no file's modification time fell in either stated window (2026-09-11 18:00-23:00 UTC or 2026-09-12 00:00-05:00 UTC); files were identified by content timestamp instead, which matched both issues' open/close windows exactly. The #104 file's content also extends to 2026-09-14 (a resumed session covering later work), but a time-windowed spot-check showed the whole-file token sum is within ~0.5% of the true #104-only window, so it was used as-is with the caveat stated on the issue.
FINDINGS/BLOCKERS: none
CANON: docs/STATE.md, docs/archive/STATE-history.md, docs/reference/site.md
NEXT-NEEDED: none — P3-B2 write routes (card #95) remains next per STATE.md §5, unaffected by this chunk
=== END ===
