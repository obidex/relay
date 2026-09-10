**Chunk 9 preflight — the 28 SQL suites measured inside the replay container: 2 of 28 pass as-is, 26 of 28 once the container has people; the last 2 fail for a real repository defect, and a second defect of the same kind turned up. Both are pinned, not hidden.**

<!-- index: chunk 9 preflight — suites in the replay container: 2/28 as-is, 26/28 with harness actors + auth.uid(); 2 excluded for replay defect D240(a); defect D240(b) pinned -->

**For the owner, in one paragraph.** CI runs 28 database test suites against the one shared live
database, so anybody's leftover rows can turn every branch red. The plan moves them into the
throwaway copy of the database that CI already builds from the repository. Measured first: as-is,
only 2 of 28 pass there — the copy has no people in it, and one sign-in helper reads a different
form than live does. With those two gaps filled in the test harness — never in a suite, never in the
product — 26 of 28 pass. The last 2 fail for a real reason: a database built from the repository
gives one administrator role four fewer permissions than the live one has. A second, smaller defect
of the same kind also appeared. Both are now named on every CI run and go red, with instructions,
the day they are fixed.

## A1 — each suite in the replay container, as the chunk asked (before any change)

`REPLAY_KEEP=1 bash scripts/replay-check.sh`, then every `supabase/tests/*.sql` in that container
with `psql -U postgres -v ON_ERROR_STOP=1 -f`. Classified by first error.

| Suite | As-is | Why (first error) | With the harness |
|---|---|---|---|
| activity_log_record_history_tests | FAIL | no active Owner member | PASS |
| activity_log_tests | FAIL | no active Owner member | **EXCLUDED** — finding (a) |
| arabic_cut_tests | PASS | — | PASS |
| catalog_supplier_tests | FAIL | no active member holding the key it borrows | PASS |
| fx_cut_tests | FAIL | no active member | PASS |
| imports_costs_tests | FAIL | finding (b) — first suite to create a shipment | PASS |
| imports_landed_cost_tests | FAIL | no active member | PASS |
| imports_milestones_tests | FAIL | no active member | PASS |
| imports_shipments_tests | FAIL | no active member | PASS |
| inventory_receipt_transfer_tests | FAIL | no active member | PASS |
| inventory_reorder_tests | FAIL | no active member | PASS |
| inventory_stock_tests | FAIL | no active member | PASS |
| permission_system_tests | FAIL | no active member holding the key it borrows | PASS |
| po_shipment_bridge_tests | FAIL | no active member | PASS |
| procurement_tests | FAIL | no active Owner member | PASS |
| product_images_tests | FAIL | sign-in helper reads a different claims form than live | PASS |
| purchase_order_3b_tests | FAIL | sign-in helper reads a different claims form than live | PASS |
| purchase_order_payments_tests | FAIL | no active member | PASS |
| purchase_order_variant_plan_tests | FAIL | no active member | PASS |
| reference_data_tests | FAIL | no active member holding the key it borrows | PASS |
| sales_dispatch_tests | FAIL | no active member | PASS |
| sales_invoice_aging_tests | FAIL | no active member | PASS |
| sales_orders_tests | FAIL | no active member | PASS |
| sales_payments_returns_tests | FAIL | no active member | PASS |
| sales_tests | FAIL | no active member | PASS |
| security_sweep_hardening_tests | FAIL | no active Owner member | PASS |
| security_sweep_s1_fixes_tests | PASS | — | PASS |
| user_management_tests | FAIL | no active Owner member | **EXCLUDED** — finding (a) |

**Totals: 2/28 as-is → 26/28 hermetic.** Fresh-build run with the finished phase: 26 run, 26 passed,
0 failed, 2 excluded, about 29 s (the build alone was about 26 s).

## Classification

- **Harness gap — the container has no people (23 suites by first error; 24 need one).** Live has
  eleven active members; a bare build has the seeded roles and no members. The harness now adds
  eleven — one per seeded role and one with none — through the real sign-up trigger, and grants
  nothing the repository does not already give those roles.
- **Harness gap — the sign-in helper (2 suites).** The container image's version reads an older
  claims form than live's; the harness installs live's version, copied from the live catalog.
- **Finding (a) — a from-scratch build gives one seeded administrator role four fewer default
  permissions than live's copy of that role** (under-grant, fail-closed). Two suites act as that role
  and are **excluded by name**, printing their reason on every run; they keep running against live
  every night. Register: **`D240`**. Mechanism off the mirror (`D235`).
- **Finding (b) — in a fresh build, the first shipment reference handed out is one a seeded demo row
  already took** (the first shipment created on a fresh installation fails once). Not a property of
  any suite: on two fresh builds it reddened two different suites, whichever created a shipment
  first. **Pinned, not excluded.** Register: **`D240`**.
- **Both findings are pinned as OPEN** before the suites run, and each pin goes red saying what to
  delete the day its fix lands. Sabotage-proven: fixed-(a) → "is FIXED", half-fixed-(a) → "CHANGED
  SHAPE: 2", fixed-(b) → "is FIXED". Both fixes are one-file seed corrections this chunk's plan did
  not name, so they are recorded, not made.

## Everything else checked before building

| | |
|---|---|
| Collaborators on this repository | `obidex` only · pending invitations **0** |
| Part B — GATE-1 hook inside the DISPATCHED lane | **Yes** — chunk 8's database smoke (#110) was dispatched and the hook refused an unpublished file there. Re-checked today: it refuses and prints the **bare** hash, never the text its check searches for |
| C1 — scratch heartbeat on the relay | **Removed**, index rebuilt, relay `976c29f`. No scratch timer, unit, state directory or parameter file on the box |
| C2 — `chunk5-t1-lane-fixes` | **Deleted** (4 commits not on `main`, tip `b0e0f42` recorded) |
| C3 — docker images | `hello-world` and `alpine:3` were **already absent**; no reference in `infra/` or `scripts/`. Three images remain, all in use |
| C4 — shellcheck at `warning` | **30 findings** over 21 scripts (the plan said 29 over 20 — one script was added since) |

```
=== RELAY ===
HEAD: 6718a45 (main) | tree: dirty (chunk 9 work in progress, not yet committed)
CI: none yet — nothing pushed
DONE: A1 measured (2/28 as-is) and every failure classified · harness actors + live auth.uid() → 26/28 hermetic on a fresh build · findings D240(a) and (b) pinned OPEN, sabotage-proven both ways · C1 relay scratch heartbeat removed (relay 976c29f) · C2 superseded branch deleted · C3 nothing to remove · B: GATE-1 hook confirmed inside the dispatched lane, bare hash re-verified
FILES: none committed yet; tracking issue #112
FINDINGS/BLOCKERS: (a) a from-scratch build under-grants one seeded administrator role by four default permissions — two suites excluded for it, still run nightly against live; (b) a fresh build's first shipment reference collides with a seeded demo row — pinned. Both are one-file seed fixes outside this chunk's named paths (D240). Shellcheck count is 30, not 29.
NEXT-NEEDED: none
=== END ===
```
