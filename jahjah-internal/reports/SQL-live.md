# SQL suites against LIVE — nightly

<!-- index: nightly SQL suites vs LIVE — PASS 30/30 in 15s -->

**Generated (UTC):** 2026-09-24T03:15:00Z
**Verdict:** **PASS** — all 30 suites passed against the live database.
**Suites from:** `obidex/jahjah-internal` `main` at `5b6467c`
**Migrations:** in step — all 83 migrations on main are applied on live, and live has none main lacks (newest `20260924000450`)
**Ran for:** 15s

This is the nightly run of the always-rollback SQL test suites against the **live** database, from
the work engine. Pull-request CI runs the same suites in a throwaway database built from the
repository; this run is the one that sees live. **Stale by more than ~26 hours = this job is not
running** (or is switched off: `/opt/jahjah/SQL_LIVE_OFF`).

| Suite | Result | Seconds |
|---|---|---|
| `activity_log_record_history_tests` | PASS | 0.5 |
| `activity_log_tests` | PASS | 0.4 |
| `arabic_cut_tests` | PASS | 0.6 |
| `catalog_supplier_tests` | PASS | 0.9 |
| `d252_daily_syp_rate_tests` | PASS | 0.4 |
| `fx_cut_tests` | PASS | 0.5 |
| `imports_costs_tests` | PASS | 0.3 |
| `imports_landed_cost_tests` | PASS | 0.3 |
| `imports_milestones_tests` | PASS | 0.3 |
| `imports_shipments_tests` | PASS | 0.3 |
| `inventory_receipt_transfer_tests` | PASS | 0.5 |
| `inventory_reorder_tests` | PASS | 0.3 |
| `inventory_stock_tests` | PASS | 0.3 |
| `permission_system_tests` | PASS | 0.3 |
| `po_shipment_bridge_tests` | PASS | 0.2 |
| `procurement_tests` | PASS | 0.5 |
| `product_images_tests` | PASS | 0.2 |
| `purchase_order_3b_tests` | PASS | 0.3 |
| `purchase_order_payments_tests` | PASS | 0.2 |
| `purchase_order_variant_plan_tests` | PASS | 0.3 |
| `reference_data_tests` | PASS | 0.3 |
| `sales_dispatch_tests` | PASS | 0.6 |
| `sales_invoice_aging_tests` | PASS | 0.7 |
| `sales_orders_tests` | PASS | 0.6 |
| `sales_payments_returns_tests` | PASS | 0.9 |
| `sales_revisions_price_floor_tests` | PASS | 1.3 |
| `sales_tests` | PASS | 0.5 |
| `security_sweep_hardening_tests` | PASS | 0.3 |
| `security_sweep_s1_fixes_tests` | PASS | 0.1 |
| `user_management_tests` | PASS | 0.3 |

**Reading a FAIL.** A suite that passes in CI and fails here means live differs from what the
repository builds, or something left rows behind in the shared database — a cancelled E2E run is the
usual one (`docs/pitfalls/infra-vps.md`). Each suite's full output is on the box in
`/opt/jahjah/sql-live/work/` and is **never published**: it can carry live values.

Stop: `touch /opt/jahjah/SQL_LIVE_OFF` · registry: `docs/runbooks/automations.md`
