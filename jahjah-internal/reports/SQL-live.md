# SQL suites against LIVE — nightly

<!-- index: nightly SQL suites vs LIVE — FAIL (main ahead of live) 42/47 -->

**Generated (UTC):** 2026-10-03T03:15:03Z
**Verdict:** **FAIL — MAIN AHEAD OF LIVE.** A migration on main has not been applied to live; 42 of 47 suites passed regardless.
**Suites from:** `obidex/jahjah-internal` `main` at `470b2c7`
**Migrations:** **MAIN AHEAD OF LIVE** — on main, never applied to live: `20261002150000`
**Ran for:** 27s

This is the nightly run of the always-rollback SQL test suites against the **live** database, from
the work engine. Pull-request CI runs the same suites in a throwaway database built from the
repository; this run is the one that sees live. **Stale by more than ~26 hours = this job is not
running** (or is switched off: `/opt/jahjah/SQL_LIVE_OFF`).

| Suite | Result | Seconds |
|---|---|---|
| `activity_log_record_history_tests` | PASS | 0.9 |
| `activity_log_tests` | **FAIL** (psql exit 3) | 0.5 |
| `arabic_cut_tests` | PASS | 0.5 |
| `cash_sessions_cash_sale_cheques_tests` | PASS | 1.2 |
| `catalog_supplier_tests` | PASS | 0.9 |
| `credit_collections_returns_tests` | PASS | 1.4 |
| `customer_price_tier_gate_tests` | PASS | 0.2 |
| `d252_daily_syp_rate_tests` | PASS | 0.3 |
| `data_status_events_tests` | PASS | 0.2 |
| `dispatch_driver_capacity_tests` | PASS | 0.4 |
| `fx_cut_tests` | PASS | 0.6 |
| `imports_costs_tests` | PASS | 0.2 |
| `imports_landed_cost_tests` | PASS | 0.5 |
| `imports_milestones_tests` | PASS | 0.3 |
| `imports_shipments_tests` | PASS | 0.3 |
| `inventory_receipt_transfer_tests` | PASS | 0.6 |
| `inventory_reorder_tests` | PASS | 0.3 |
| `inventory_stock_tests` | PASS | 0.3 |
| `landed_value_true_up_tests` | PASS | 1.3 |
| `permission_system_tests` | **FAIL** (psql exit 3) | 0.2 |
| `platform_access_fixes_tests` | **FAIL** (psql exit 3) | 0.2 |
| `po_shipment_bridge_tests` | PASS | 0.3 |
| `procurement_tests` | **FAIL** (psql exit 3) | 0.1 |
| `product_images_tests` | PASS | 0.2 |
| `purchase_order_3b_tests` | PASS | 0.3 |
| `purchase_order_payments_tests` | PASS | 0.2 |
| `purchase_order_variant_plan_tests` | PASS | 0.3 |
| `receipt_reversal_tests` | PASS | 0.4 |
| `receipt_short_arrival_tests` | PASS | 0.6 |
| `receive_shipment_atomic_tests` | PASS | 0.4 |
| `receiving_pieces_per_unit_tests` | PASS | 0.7 |
| `receiving_po_open_quantity_tests` | PASS | 1.4 |
| `receiving_reconciliation_tests` | PASS | 0.8 |
| `receiving_verifier_fixes_tests` | PASS | 0.7 |
| `reference_data_tests` | PASS | 0.3 |
| `role_keys_you_hold_tests` | PASS | 0.2 |
| `sales_dispatch_tests` | PASS | 0.6 |
| `sales_invoice_aging_tests` | PASS | 0.9 |
| `sales_orders_tests` | PASS | 0.7 |
| `sales_payments_returns_tests` | PASS | 1.7 |
| `sales_revisions_price_floor_tests` | PASS | 1.4 |
| `sales_tests` | PASS | 0.4 |
| `security_sweep_hardening_tests` | PASS | 0.2 |
| `security_sweep_s1_fixes_tests` | PASS | 0.1 |
| `suppliers_crm_tests` | PASS | 1.1 |
| `temp_password_db_block_tests` | **FAIL** (psql exit 3) | 0.2 |
| `user_management_tests` | PASS | 0.3 |

**Reading a FAIL.** A suite that passes in CI and fails here means live differs from what the
repository builds, or something left rows behind in the shared database — a cancelled E2E run is the
usual one (`docs/pitfalls/infra-vps.md`). Each suite's full output is on the box in
`/opt/jahjah/sql-live/work/` and is **never published**: it can carry live values.

Stop: `touch /opt/jahjah/SQL_LIVE_OFF` · registry: `docs/runbooks/automations.md`
