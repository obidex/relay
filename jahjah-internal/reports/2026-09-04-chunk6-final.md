# chunk 6 — MERGED. `f82f7cc` on `main`, production READY, the mirror serving the new canon.

**Chunk 6 is done.** The hardening migration was applied and verified on 2026-09-04; this half — its
tests, its tooling and the canon around it — is now on `main`. The post-merge run is green, the
production deploy is READY at the merge hash, and the publicly-served copy of `docs/STATE.md` is
byte-for-byte the merged file.

*(Filed under the 2026-09-04 date to sit with the rest of this chunk's reports; the merge itself
happened at 06:17Z on 2026-09-05.)*

## The landing

| | |
|---|---|
| **Merge hash** | **`f82f7cc1171076616fc2391b4965c0f773eb53ff`** |
| PR | **#101**, squash-merged 2026-09-05T06:17:26Z |
| `main` | `9c0ed84` → `f82f7cc` |
| Pre-merge `ci-ok` | green — all 11 checks, run `33924366586` |
| **Post-merge `main` run** | **`33949409654` — success**, `ci-ok` green, no retry needed |
| **Vercel production** | **READY** at `f82f7cc` (`dpl_DPRPZQzJK52rMJwFZ7PF4FaCCdDK`) |
| Mirror check | `/internal/docs/docs/STATE.md` → HTTP 200, **byte-identical to the merged file** |
| Branch | `chunk6-hardening` deleted on origin and locally |
| `chunk5-t1-lane-fixes` | untouched, as required |

`tier3-guard` shows *skipped* on the post-merge run — correct, it is `pull_request`-only. It passed on
the PR, which is where it binds.

## What shipped

`20260903120000_sale_issue_guard_bind_and_audit_actor`, SHA-256
`07c44665ac520417c0d0391192cc6251aaafaf156b044024f48621468fd90121`.

- **`D225`(a)** the sale-issue guard binds the dispatch line to the order line, the variant and the
  location, and caps the issue at that line's own quantity (`JHI11`) · **(b)** `EXECUTE` revoked from
  `public, anon, authenticated` on the guard and on `rls_auto_enable()` · **(c)** private SQLSTATEs
  `JHI11`/`JHI12` · **(d)** the guard's row lock changed · **(e)** explicit `ON DELETE RESTRICT` ·
  **(f)** the writer checks its own INSERT's row count · **(g)** KEEP, the column name stands.
- **`D161`, INSERT/DELETE half only** — the payment audit row is now written in system context with a
  null actor. The UPDATE half is **not** closed and is `D234`(4), with a tripwire.
- **`D162`** closed. **`D229`**'s carried grant-audit hardening landed in full.
- **Canon:** `D232`, `D233`, `D234` (twelve open residuals), two pitfalls files, the backup runbook,
  and `CLAUDE.md` §13.18.

## Measured, before → after

**Residual 4 of 4 → 0 of 4.** Every way of misusing a dispatch line — unrelated variant, unrelated
location, another order line's dispatch line, more than the line's own quantity — was ACCEPTED before
and is refused now, each by the check that names it. Each arm had its own dispatch line so the
one-issue-per-line index could never be the control that answered.

**Deadlock reproduced → gone.** `D225`(d) was real: two sessions produced a genuine `40P01` with the
guard named in the stack, on the one path the guard exists to police. It no longer occurs, and mutual
exclusion survives.

## Probe disclosure

`scripts/probe-sale-issue-deadlock.sh` ran twice against the shared database earlier in this chunk on
a safety premise that was a converse error — a seeded fixture does not prove a development database,
because the seeds are applied to the live project. Both runs were `begin … rollback` and the script
reads back that zero rows survived; verified. It did hold row locks on a real order line for a few
seconds. Its gate is now an explicit acknowledgement plus a readback in **both** sessions, fail-closed
on timeout as well as on a wrong value, and it is not wired into CI.

## What chunk 6 leaves open — `D234`, twelve items

Mechanism for every one lives in the off-mirror pitfalls files and runbooks; the register carries
shape, priority, close-trigger and a pointer.

**`D234`(6) is a regression this change introduced**, not a pre-existing residual: closing `D161`'s
INSERT/DELETE half made the payment audit writer cross-table under `ENABLE ALWAYS`, so a data-only
restore fabricates one null-actor booking per restored payment. The operational mitigation is in
`docs/runbooks/backup.md` §2; the real fix is `D234`(3)'s provenance column.

**Two items outrank everything this chunk closed, and should shape the next one:**

- **(7)** — the ledger's largest uncontrolled money write is open, and an existing suite does not
  merely miss it: it performs that write and a later test folds the result into an asserted money
  figure. **Its fix is a tripwire AND a migration** — landing only the tripwire would not close it.
- **(8)** — a money/stock reconciliation break reproduced in the shipped guard.

**And a warning for whoever writes that migration:** do not land `D234`(10)'s uniqueness idea
alongside (4). Two panel seats independently showed they collide and would refuse a second legitimate
edit.

## How this was reviewed

Nine rounds of the four-agent panel. The migration itself was called sound by every seat in every
round; what kept failing was the canon written around it. **Six consecutive rounds caught the
mechanism of an open defect being published onto the public mirror**, each time after fixing exactly
where the previous round pointed — the last instance being one where the on-mirror copy was more
detailed than the off-mirror one. The fix that finally held was structural rather than editorial, and
this PR was deliberately opened as a draft and held for a disclosure read rather than self-approved.

The panel also produced the two findings above, neither of which this chunk was looking for.

```
=== RELAY ===
HEAD: f82f7cc1171076616fc2391b4965c0f773eb53ff | tree: clean
CI: post-merge main run 33949409654 — SUCCESS, ci-ok green (pre-merge PR run 33924366586, all 11 checks green)
DONE: PR #101 squash-merged; migration 20260903120000 + tests + D229 audit hardening + canon (D232/D233/D234) on main; Vercel production READY at the merge hash; /internal/docs/docs/STATE.md serves the merged bytes; branch chunk6-hardening deleted
FILES: 17 in one squash commit (2 new: the migration + scripts/probe-sale-issue-deadlock.sh)
FINDINGS/BLOCKERS: none outstanding for this chunk. D234 carries twelve open residuals; (6) is a regression this change introduced, and (7) — the ledger's largest uncontrolled money write, open AND asserted as correct by an existing test — is larger than anything chunk 6 closed and needs both a tripwire and a migration.
NEXT-NEEDED: none. The UI/UX rebuild spec session is next; D234(7) and (8) should scope the migration chunk before it.
=== END ===
```
