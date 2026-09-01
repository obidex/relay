# Chunk 1 — final report

<!-- index: chunk 1 FINAL — 4 of 5 tasks merged, T5 blocked with proof; one M3 incident self-caught and corrected -->

**`main` @ `a745e45`, clean, post-merge CI green.** Four PRs merged, one task blocked by design.
Executed unattended on the VPS work engine. **Model: Opus 5 (1M context), xhigh** — the prompt
specified Opus 4.8; reported per the pilot rule.

## 1. Per-task outcome

| Task | Tier | Outcome | Merge | Post-merge CI |
|---|---|---|---|---|
| **T1** money anchor → `ENABLE ALWAYS` | 3 | **MERGED** | PR #77 · `fab6902` | green |
| **T2** canon fixes (website-SKU claim, model routing) | 1 | **MERGED** (same branch) | PR #77 · `fab6902` | green |
| **T3** generated schema types + drift gate | 2 | **MERGED** | PR #78 · `ca2c75e` | green |
| **T4** replay check as a CI job | 2 | **MERGED** | PR #79 · `2f8ab7f` | green |
| **T5** duplicate sale-movement backstop | 3 | **BLOCKED — no SQL applied** | — | — |
| chunk close | — | **MERGED** | PR #80 · `a745e45` | green |

**Decisions:** `D208` closed · `D209` closed · `D174`'s closing plan **superseded, not closed** ·
`D213`–`D219` written.

## 2. What actually changed

**The money anchor's guard now holds in a mode where it previously did not.** One migration, one
statement, exactly as approved. Backup taken and verified before anything ran; the end state read back
from the catalog rather than inferred; behaviour checked in both modes. The seed that establishes the
anchor was moved to match in the same change — they are a matched pair, and reverting either one alone
now reds two independent assertions.

**Four new assertions, each sabotage-verified** against the state it exists to catch. One of them
pins a platform assumption the whole design rested on, which had been recorded only as prose from a
one-time measurement. It is asserted behaviourally, deliberately: the obvious catalog helper reports
"cannot" for an actor that demonstrably can, so a check built on it would have been green and
worthless.

**Two new CI gates.** Schema types are committed and cannot silently go stale; the whole database is
now rebuilt from the repository on every pull request. The second closes a contradiction the canon had
been carrying openly — that a locally-run check nothing automates is not a gate — and it makes the
money anchor's behavioural proof automatic rather than manual.

**A false claim was removed from four canon files.** The ERP and the public website are not connected;
verified in both directions. The website's only occurrence of the word is a different identifier
entirely, which is exactly how such a claim survives a casual check.

## 3. T5 — blocked, and why that is the correct outcome

**The approved SQL would have broken a shipped feature.** It assumed at most one issue movement per
order line; the dispatch function writes one **per dispatch call**, and partial dispatch is supported
and covered by a committed test. **Proven by execution:** the approved indexes, created inside that
committed suite, fail it at the test literally titled *"partial then completing dispatch"*.

Two corrections to the decision register fell out of that: the return half of the block is actually
sound, and the fallback the chunk named as plan B **is already shipped** — so the real remaining gap
is narrower than recorded. The corrected design is written up in full and **not applied**, because it
re-crosses the approval gate.

**This is the chunk working as intended.** The premise check was the first thing done, before any
change, and it is the reason no bad SQL reached the database.

## 4. The incident, stated plainly

**Two unapproved indexes reached the live database and were removed within minutes.** While proving
T5, a malformed probe placed its DDL outside the test file's transaction; the tool runs
statement-at-a-time, so they committed. They were exactly the objects the same report proves must not
exist, and while present they would have made any partial dispatch fail.

Detected in my own post-probe verification, dropped, and restoration confirmed four ways — including a
freshly regenerated reference that differs from the committed copy **only in its timestamp line**.
Reported under the rule that anything reaching the live database outside the gate is stopped on and
reported, never quietly absorbed. **Nothing needs undoing; this needs knowing.**

The lesson is not "be careful". The register already says the tool is not transactional, and I applied
that correctly all session when writing migrations — then relied on a file's transaction to protect a
probe injected outside it. **A probe that creates anything is a write.** What caught it was the habit
of reading state back afterwards, not care while writing.

## 5. Findings worth the owner's attention

- **No CI job in this repository is a hard merge gate.** Branch protection is a paid feature and the
  API refuses it here, so nothing on the platform blocks a merge past a red check — every job is
  enforced by discipline. Pre-existing, now stated plainly in the canon and in the parked list.
- **Two CI failures happened, both on jobs added this chunk, and neither was fixed by re-running.**
  One was an unretried network dependency; one a readiness budget too short for a loaded runner. Both
  had the same deeper defect: **the failure said nothing about its cause.** Both now surface the
  underlying error. A gate whose failures are undiagnosable teaches people to click through red.
- **The review panel found more than the SQL.** The one-line migration was correct from the start;
  everything caught was in the prose around it — canon describing the change as unshipped, a residual
  stated backwards, and sensitive mechanism written into a file that is served publicly. It also got
  one recommendation **wrong** in a way that would have broken the gate, which is the case for
  verifying a panel's premise rather than its verdict.
- **The prompt specified Opus 4.8; this ran on Opus 5.**

## 6. Carried

`D215` (priority) the payment-side triggers were not promoted with the anchor · `D218` the corrected
sale-movement backstop needs a design authorized · `D214(b)` a superseding comment still owed inside
the migrations folder · `D216` app-side adoption of the generated types · the Node action bump, now
overdue: this chunk touched CI twice without taking it, deliberately, to stay on scope.

## 7. The one decision waiting

**Which corrected design to authorize for `D218`** — **(A)** key the backstop on the dispatch line
(the true grain) via a new nullable reference, or **(B)** guard on insert so the ledger can never
drain more than the dispatch record justifies. **Recommend B as the control, A as the optional
structural backstop.** Either re-crosses the approval gate. Not urgent as live risk — there are zero
sale movements today — but urgent in the sense the original decision meant: an append-only ledger
cannot be corrected by deletion, so it wants closing before real stock data exists.

## 8. RELAY BLOCK

*Written into this report at chunk 2 · T0, 2026-09-01. The block was not captured in the file when
chunk 1 published it; every field below is **reconstructed from the merged commits, the post-merge CI
run and this report's own sections** — it is accurate, not verbatim.*

```
=== RELAY ===
HEAD: a745e45 | tree: clean
CI: pass — post-merge main run 33502873616, success, 2026-09-01T11:30–11:39Z
DONE: T1 money anchor promoted to ENABLE ALWAYS + seed matched-pair alignment (PR #77, fab6902)
      T2 canon fixes — false website-SKU claim removed, model routing corrected (PR #77, fab6902)
      T3 generated schema types committed + hermetic CI drift gate (PR #78, ca2c75e)
      T4 from-scratch DB replay wired in as a CI job (PR #79, 2f8ab7f)
      T5 BLOCKED by design — approved D174 index breaks partial dispatch, proven by execution; no SQL applied
      chunk close — D218/D219 written, STATE rewritten, register updated (PR #80, a745e45)
FILES: 17 files, +4535/-83 across the chunk. Notable: supabase/migrations/20260901120000_currency_anchor_enable_always.sql,
      supabase/seed/05_currencies.sql, supabase/tests/reference_data_tests.sql, scripts/replay-check.sh,
      scripts/gen-types.sh, src/lib/supabase/database.types.ts, .github/workflows/ci.yml,
      docs/DECISIONS.md, docs/STATE.md, docs/ROADMAP.md, docs/reference/*
FINDINGS/BLOCKERS: T5 blocked — the approved index keys on the wrong grain (D218); corrected design not applied.
      M3 incident, self-caught and corrected — two unapproved indexes reached the live database for ~4 minutes
      during the T5 proof and were dropped; restoration verified four ways (D219).
      No CI job in this repository is a hard merge gate — branch protection is Pro-gated and 403s here.
      Two CI failures on newly added jobs, neither fixed by re-running; both had undiagnosable failure output,
      now fixed to surface the underlying error.
      Pilot rule: the prompt specified Opus 4.8; this ran on Opus 5, xhigh.
NEXT-NEEDED: which corrected D218 design to authorize — (A) key the backstop on the dispatch line via a new
      nullable reference, or (B) guard on INSERT so the ledger cannot drain more than the dispatch record
      justifies. Recommend B as the control, A as the optional structural backstop.
=== END ===
```
