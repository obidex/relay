# SQL suites against LIVE — nightly

<!-- index: nightly SQL suites vs LIVE — FAIL (main ahead of live) 47/53 -->

**Generated (UTC):** 2026-10-04T03:15:00Z
**Verdict:** **FAIL — MAIN AHEAD OF LIVE.** A migration on main has not been applied to live; 47 of 53 suites passed regardless.
**Suites from:** `obidex/jahjah-internal` `main` at `135e6b5`
**Migrations:** **MAIN AHEAD OF LIVE** — on main, never applied to live: `20261003120000`
**Ran for:** 33s

This is the nightly run of the always-rollback SQL test suites against the **live** database, from
the work engine. Pull-request CI runs the same suites in a throwaway database built from the
repository; this run is the one that sees live. **Stale by more than ~26 hours = this job is not
running** (or is switched off: `/opt/jahjah/SQL_LIVE_OFF`).

| Suite | Result | Seconds |
|---|---|---|
| `activity_log_record_history_tests` | PASS | 1.0 |
| `activity_log_tests` | PASS | 0.6 |
| `arabic_cut_tests` | PASS | 0.6 |
| `cash_drawer_integrity_tests` | PASS | 1.8 |
| `cash_sessions_cash_sale_cheques_tests` | PASS | 0.9 |
| `catalog_identity_tests` | PASS | 1.3 |
| `catalog_supplier_tests` | **FAIL** (psql exit 3) | 0.2 |
| `credit_collections_returns_tests` | **FAIL** (psql exit 3) | 0.3 |
| `credit_gate_and_holds_tests` | **FAIL** (psql exit 3) | 0.3 |
| `customer_price_tier_gate_tests` | PASS | 0.3 |
| `d252_daily_syp_rate_tests` | PASS | 0.3 |
| `data_status_events_tests` | PASS | 0.2 |
| `dispatch_driver_capacity_tests` | PASS | 0.5 |
| `fx_cut_tests` | PASS | 0.6 |
| `imports_costs_tests` | PASS | 0.3 |
| `imports_landed_cost_tests` | PASS | 0.6 |
| `imports_milestones_tests` | PASS | 0.5 |
| `imports_shipments_tests` | PASS | 0.6 |
| `inventory_receipt_transfer_tests` | PASS | 0.6 |
| `inventory_reorder_tests` | PASS | 0.3 |
| `inventory_stock_tests` | PASS | 0.3 |
| `landed_value_true_up_tests` | PASS | 1.1 |
| `payments_cheques_rules_tests` | PASS | 1.5 |
| `permission_system_tests` | PASS | 0.3 |
| `platform_access_fixes_tests` | PASS | 0.3 |
| `platform_minor_fixes_tests` | PASS | 0.2 |
| `po_shipment_bridge_tests` | PASS | 0.2 |
| `procurement_tests` | PASS | 0.5 |
| `product_images_tests` | PASS | 0.2 |
| `purchase_order_3b_tests` | PASS | 0.3 |
| `purchase_order_payments_tests` | PASS | 0.3 |
| `purchase_order_variant_plan_tests` | PASS | 0.3 |
| `receipt_reversal_tests` | **FAIL** (psql exit 3) | 0.2 |
| `receipt_short_arrival_tests` | PASS | 0.8 |
| `receive_shipment_atomic_tests` | PASS | 0.4 |
| `receiving_pieces_per_unit_tests` | PASS | 1.9 |
| `receiving_po_open_quantity_tests` | PASS | 1.3 |
| `receiving_reconciliation_tests` | PASS | 1.2 |
| `receiving_verifier_fixes_tests` | PASS | 0.7 |
| `reference_data_tests` | PASS | 0.3 |
| `role_keys_you_hold_tests` | PASS | 0.2 |
| `sales_dispatch_tests` | **FAIL** (psql exit 3) | 0.1 |
| `sales_invoice_aging_tests` | PASS | 0.9 |
| `sales_orders_tests` | **FAIL** (psql exit 3) | 0.3 |
| `sales_payments_returns_tests` | PASS | 1.2 |
| `sales_revisions_price_floor_tests` | PASS | 1.4 |
| `sales_tests` | PASS | 0.4 |
| `security_sweep_hardening_tests` | PASS | 0.3 |
| `security_sweep_s1_fixes_tests` | PASS | 0.1 |
| `suppliers_crm_tests` | PASS | 1.5 |
| `temp_password_db_block_tests` | PASS | 0.2 |
| `temp_password_reissue_tests` | PASS | 0.2 |
| `user_management_tests` | PASS | 0.3 |

**Reading a FAIL.** A suite that passes in CI and fails here means live differs from what the
repository builds, or something left rows behind in the shared database — a cancelled E2E run is the
usual one (`docs/pitfalls/infra-vps.md`). Each suite's full output is on the box in
`/opt/jahjah/sql-live/work/` and is **never published**: it can carry live values.

Stop: `touch /opt/jahjah/SQL_LIVE_OFF` · registry: `docs/runbooks/automations.md`
