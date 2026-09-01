# Chunk 2 — final report

<!-- index: chunk 2 FINAL — T0 done; T1+T2 BLOCKED with no SQL applied; both GATE-1 packages prepared for a session on the database lane -->

**One of three tasks delivered. Nothing applied to any database, nothing merged, no migration file
committed.** Both blocked tasks come back with their premises confirmed and their GATE-1 packages
written, so the next attempt spends its time applying rather than deriving. **Model: Opus 5, xhigh** —
the prompt specified Opus 4.8; reported under the §17 pilot rule.

## 1. Per-task outcome

| Task | Tier | Outcome | Evidence |
|---|---|---|---|
| **T0** close chunk 1 | 1 | **DONE** | PR #80 was already merged (`a745e45`, 11:30:52Z) with post-merge CI green (run `33502873616`). Chunk 1's RELAY BLOCK is now written into `2026-09-01-chunk1-final.md` §8, labelled reconstructed. |
| **T1** `D218` sale double-drain | 3 | **BLOCKED — two independent rules** | No database lane; **and** the approved guard is defective. Corrected block published. |
| **T2** `D215` payment-guard promotion | 3 | **BLOCKED — lane only** | Premise fully confirmed, action unmodified, alignment located. Ready to run. |
| chunk close | 1 | canon updated on the chunk branch, **not merged** | `D220`, `D221`, `STATE.md`, the roadmap register. |

**Decisions:** `D220` and `D221` written · `D215` stays **OPEN** · `D218` stays **OPEN** and now
re-crosses GATE 1 · `D214(b)` still owed.

## 2. What actually happened

**The chunk was routed to the wrong box.** Chunk 1 ran on the VPS work engine, which owns the database
lane. This session ran in a container with no credentials in either lane — so the mandatory `pg_dump`
backup, the apply, the catalog read-back, the SQL suites and the live reference regeneration were all
unreachable. That alone stops both migrations, and it stops them cleanly: the correct response is a
routing correction, not a workaround.

**CI is not a substitute, and it was worth checking rather than assuming.** The `sql` job runs the
suites against the shared **live** dev database. A migration committed but never applied there makes
every new assertion red, so a "commit it and let CI prove it" route cannot reach green and GATE 2 can
never be crossed. What that route would actually leave behind is a red branch carrying an unapplied
migration — indistinguishable, to the next reader, from work in progress. Nothing was committed to the
migrations folder.

**Then the T1 premise check found something the lane would not have saved us from.** The approved
guard function declares no `SECURITY` clause, so it is `SECURITY INVOKER`, and it reads three tables
whose SELECT gates the guard's own target actor does not hold. For a `manage_inventory`-only actor —
the exact actor `D218` names as the residual gap — both aggregates read zero rows, the comparison is
false, and the over-drain is admitted. On the legitimate path the trigger fires inside a
`SECURITY DEFINER` RPC and works correctly. **The control is live on the honest path and blind on the
attack path.** The fix is three added lines, which is a semantic change, which is a deviation-stop.

**T2, by contrast, is clean.** The guard set is exactly the two triggers the register names, both in
the weaker state, and the approved one-line promotion applies to each as written. Nothing needed the
prompt's "flag it and continue" clause.

## 3. Findings worth the owner's attention

- **A guard can be correct in every predicate and still be blind.** T1's SQL had no wrong identifier,
  no wrong table and no wrong predicate. What it had was a privilege assumption, and it inverted the
  control. Both halves of the reasoning were already written down in this project — that this ledger's
  write key is not its read key, and that a function with no `SECURITY` clause is INVOKER. The failure
  was not knowing them; it was not putting them together.
- **The test that would have caught it is not the obvious one.** Testing the guard through the dispatch
  RPC passes — the RPC is `SECURITY DEFINER`, so the guard works there. Only a raw INSERT as a
  `manage_inventory`-only actor separates the corrected guard from the approved one. That assertion is
  now specified.
- **One premise in the prompt was imprecise.** It stated the ledger has zero rows; it has five, with
  zero sale movements. Nothing downstream changes — no backfill, and every proposed constraint and
  index validates against those five — but "zero rows" also implies the table can be rewritten freely,
  which is a different claim.
- **`docs/STATE.md` was stale by one commit**, recording `2f8ab7f` as the `main` HEAD. That line was
  written inside PR #80 and so could not name its own merge commit. Corrected.
- **The prompt specified Opus 4.8; this ran on Opus 5.**

## 4. Commits

| Repo | Branch | What |
|---|---|---|
| `obidex/relay` | `main` | five report files: this one, the preflight, T0, BLOCKED, and the RELAY BLOCK added to chunk 1's final report — plus the rebuilt `INDEX.md` |
| `obidex/jahjah-internal` | `claude/chunk-2026-09-01-migrations-pbc83g` | canon only: `D220`, `D221`, `STATE.md`, the roadmap register. **Pushed, not merged, and no PR opened** — the chunk is in a stop state, so the merge is the owner's call. |

No migration file, no schema change, no application code, no deploy.

## 5. Carried

`D215` — **specified and ready**; needs the database lane and nothing else · `D218` — needs GATE 1
re-crossed on the corrected block, plus one judgement call on the raise message and, optionally, a
naming decision · `D214(b)` the superseding comment still owed inside `supabase/migrations/`, still
best carried by `D215`'s migration · `D216` app-side adoption of the generated types · the Node-20 →
Node-24 action bump, now two chunks overdue.

## 6. The one decision waiting

**Re-run T2 and T1 from the database lane, in that order.** T2 needs no new approval beyond its
GATE-1 file. T1 needs GATE 1 re-crossed on the corrected block in
`2026-09-01-chunk2-BLOCKED.md` §3.2, plus a one-word answer on whether the guard's error message may
disclose the dispatched/issued quantities to an actor who cannot read those tables — recommended:
no, keep the numbers out.
