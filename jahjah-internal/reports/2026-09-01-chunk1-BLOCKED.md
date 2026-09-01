# Chunk 1 · T5 — BLOCKED. The approved SQL would break a shipped feature.

<!-- index: T5 BLOCKED — approved D174 index would break partial dispatch, PROVEN by execution; plus an M3 incident I caused and corrected -->

**Status: BLOCKED, no SQL applied.** T5's premise is false, which the chunk makes a
deviation-stop. **Two things in this report need the owner's attention: the block itself, and an
incident I caused while proving it.** The incident is second only because the block explains it.

## 1. The premise, and why it is false

T5 asked me to verify, first: *at most one `sale_issue` movement per order line — by writer-function
design, in existing data, and in E2E fixtures.* It fails on the first of those, which is the one that
matters.

**`dispatch_sales_order` writes one `sale_issue` per order line PER DISPATCH CALL, and partial
dispatch is a shipped, supported feature.** The function validates each line against its *remaining*
quantity and deliberately leaves the order open when a backorder remains. Two partial dispatches
against the same line therefore produce two movements carrying the same order-line reference — which
the approved unique index rejects.

**This is not a reading of the code. I proved it by execution.** I created the approved indexes inside
the committed dispatch suite and ran it. It fails at **TEST 2 — titled "HAPPY PATH: partial then
completing dispatch"** — on the completing dispatch:

```
ERROR: duplicate key value violates unique constraint
       "stock_movements_one_issue_per_order_item"
```

So the approved SQL would turn a committed suite red **and break backorders in the live product.**
Adding the variant column — the form the original decision floats — does not help: both partial
dispatches carry the same variant too.

**The return half of the approved block is sound** — each return line creates a fresh row and links
its movement to that, so one-per-return-item holds by construction. But applying half of an approved
two-statement block is substitute SQL, which the chunk forbids outright. It goes back through the
gate with the corrected design.

**Live data was never the problem:** there are zero `sale_issue` and zero `sale_return` rows today.
The defect is entirely forward-compatibility with a feature that already ships.

## 2. The fallback named in the chunk is ALREADY SHIPPED

The chunk's fallback was *"reject sale movement types via the generic writer path."* **That is done and
has been for some time.** The generic ledger writer already refuses both sale movement types with
their own dedicated error codes, exactly as it refuses transfers, and existing suites cover both.

So the residual gap is narrower than the decision record implies: it is **only** the raw
insert-through-the-API path by a holder of the ledger write key. Worth correcting in the register,
because a follow-up that proposes work already done wastes the next session.

## 3. The corrected design — described, NOT applied

**The approved index keys on the wrong grain.** The order line is not the unit of issue; the
**dispatch line** is. Two candidate fixes, both of which re-cross the approval gate:

**Option A — key on the true grain (structural).** Add a nullable reference from the ledger row to the
dispatch line, have the dispatch RPC populate it, and put the partial-unique index on *that*. Exactly
one issue movement per dispatch line, with partial dispatch unaffected. Notes: the backfill is trivial
today because there are no rows — which is precisely why the original decision marked this urgent,
*before real stock data exists*. It also adds a foreign key, so it must be paired in the same change
with a repo-wide sweep for API embeds of that parent, or a previously-unambiguous embed starts
failing.

**Option B — enforce the actual invariant (behavioural).** A guard on insert asserting that the total
issued against an order line never exceeds the total the dispatch record says was dispatched for it.
This is the invariant the original decision actually cares about — *the ledger must never drain more
than the dispatch record justifies* — it tolerates partial dispatch by construction, needs no schema
change, and covers the return side symmetrically.

**Recommendation: B as the control, A as the optional structural backstop.** B expresses the rule;
A makes a duplicate impossible rather than merely rejected. **Whichever is chosen, note that a new
guard on this table is a collateral-breakage risk for every other suite that writes to it** — that
sweep is part of the work, not an afterthought.

## 4. THE INCIDENT — two unapproved indexes reached the live database, and I removed them

**What happened.** My first attempt at the proof above was malformed: it inserted the two `create
unique index` statements **before** the test file's transaction opened instead of inside it. `psql -f`
is autocommit, statement-at-a-time, so **both indexes committed to the live database** before the
malformed script failed on a syntax error.

**Why it is serious.** They were live for roughly four minutes on the shared database; they are
exactly the objects the rest of this report proves must not exist; and while present they would have
made any partial dispatch fail. They appear in no migration and in no generated reference — schema
objects with no provenance.

**What I did.** Detected it in my own verification step (the check that the probe had left no trace),
dropped both indexes, and confirmed the restoration four ways: the index list is back to the eight the
reference records; the committed dispatch suite passes again end to end; the reference-data suite
passes; and a freshly regenerated reference differs from the committed one **only in its timestamp
line**, so the live schema is byte-identical to what the repository records.

**Reporting it rather than absorbing it.** The project's rule is that anything applied to the live
database outside the approval gate is stopped on and reported for after-the-fact ratification — *it is
reported, never repeated.* **Nothing here needs the owner to undo anything; it needs him to know it
happened.**

**The lesson, which is already canon and which I still walked into.** The register records, in as many
words, that `psql -f` is not a transaction. I knew that, applied it correctly when writing migrations
all session, and then relied on a test file's own transaction to protect a probe I had injected
*outside* it. **A probe deserves the same rollback discipline as a suite** — and the discipline that
caught this was not care while writing it, but the habit of verifying afterwards that a read-only
probe really did leave nothing behind. That check is the only reason this is a four-minute incident.

## 5. Where the chunk stands

T1, T2, T3 and T4 are merged and green and stay merged; nothing is reverted. This stop ends the
chunk. The next session needs one decision: **which corrected design to authorize for the duplicate
sale-movement backstop, A or B.**
