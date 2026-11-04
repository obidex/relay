# SQL suites against LIVE — nightly

<!-- index: nightly SQL suites vs LIVE — PASS 90/90 in 81s -->

**Generated (UTC):** 2026-11-04T03:15:02Z
**Verdict:** **PASS** — all 90 suites passed against the live database.
**Suites from:** `obidex/jahjah-internal` `main` at `d29a9ac`
**Migrations:** in step — all 153 migrations on main are applied on live, and live has none main lacks (newest `20261015110000`)
**Browser runs on the shared database:** none open
**Ran for:** 81s

This is the nightly run of the always-rollback SQL test suites against the **live** database, from
the work engine. Pull-request CI runs the same suites in a throwaway database built from the
repository; this run is the one that sees live. **Stale by more than ~26 hours = this job is not
running** (or is switched off: `/opt/jahjah/SQL_LIVE_OFF`).

| Suite | Result | Seconds |
|---|---|---|
| `activity_log_record_history_tests` | PASS | 0.9 |
| `activity_log_tests` | PASS | 0.7 |
| `arabic_cut_tests` | PASS | 0.6 |
| `cash_drawer_integrity_tests` | PASS | 2.0 |
| `cash_rate_today_tests` | PASS | 0.4 |
| `cash_refund_state_tests` | PASS | 0.9 |
| `cash_sessions_cash_sale_cheques_tests` | PASS | 1.0 |
| `catalog_identity_tests` | PASS | 1.3 |
| `catalog_supplier_tests` | PASS | 1.0 |
| `confirm_sales_order_draft_tests` | PASS | 0.9 |
| `credit_collections_returns_tests` | PASS | 1.8 |
| `credit_gate_and_holds_tests` | PASS | 1.5 |
| `customer_price_tier_gate_tests` | PASS | 0.3 |
| `d252_daily_syp_rate_tests` | PASS | 0.3 |
| `damage_grade_history_tests` | PASS | 0.5 |
| `data_status_events_tests` | PASS | 0.2 |
| `dispatch_driver_capacity_tests` | PASS | 0.5 |
| `draft_save_rejudges_lines_tests` | PASS | 0.7 |
| `fx_cut_tests` | PASS | 0.6 |
| `imports_costs_tests` | PASS | 0.3 |
| `imports_landed_cost_tests` | PASS | 0.4 |
| `imports_milestones_tests` | PASS | 0.5 |
| `imports_shipments_tests` | PASS | 0.4 |
| `inventory_receipt_transfer_tests` | PASS | 0.7 |
| `inventory_reorder_tests` | PASS | 0.4 |
| `inventory_stock_tests` | PASS | 0.3 |
| `landed_value_true_up_tests` | PASS | 1.3 |
| `maintenance_locations_tests` | PASS | 0.8 |
| `member_roles_one_transaction_tests` | PASS | 0.3 |
| `members_page_tests` | PASS | 0.9 |
| `payments_cheques_rules_tests` | PASS | 1.4 |
| `payment_unapply_one_transaction_tests` | PASS | 0.6 |
| `permission_descriptions_tests` | PASS | 0.2 |
| `permission_system_tests` | PASS | 0.4 |
| `platform_access_fixes_tests` | PASS | 0.3 |
| `platform_minor_fixes_tests` | PASS | 0.2 |
| `po_add_payment_lock_tests` | PASS | 0.4 |
| `po_payments_planned_rule_tests` | PASS | 0.4 |
| `po_schedule_one_transaction_tests` | PASS | 0.4 |
| `po_shipment_bridge_tests` | PASS | 0.3 |
| `procurement_tests` | PASS | 0.5 |
| `product_images_tests` | PASS | 0.2 |
| `purchase_order_3b_tests` | PASS | 0.3 |
| `purchase_order_payments_tests` | PASS | 0.3 |
| `purchase_order_payment_summaries_tests` | PASS | 0.8 |
| `purchase_order_variant_plan_tests` | PASS | 0.3 |
| `receipt_applies_shown_orders_tests` | PASS | 0.7 |
| `receipt_reversal_tests` | PASS | 0.5 |
| `receipt_short_arrival_tests` | PASS | 0.6 |
| `receivables_paging_tests` | PASS | 8.1 |
| `receive_shipment_atomic_tests` | PASS | 0.5 |
| `receiving_pieces_per_unit_tests` | PASS | 0.8 |
| `receiving_po_open_quantity_tests` | PASS | 1.5 |
| `receiving_reconciliation_tests` | PASS | 0.9 |
| `receiving_verifier_fixes_tests` | PASS | 0.7 |
| `record_member_names_tests` | PASS | 0.4 |
| `reference_data_tests` | PASS | 0.4 |
| `resolve_sale_prices_tests` | PASS | 1.8 |
| `return_maintenance_tests` | PASS | 1.0 |
| `return_refunds_tests` | PASS | 1.5 |
| `role_keys_you_hold_tests` | PASS | 0.2 |
| `sale_price_book_page_tests` | PASS | 0.8 |
| `sale_price_ranges_discounts_tests` | PASS | 1.0 |
| `sale_price_ranges_tables_tests` | PASS | 0.3 |
| `sales_credit_marks_trail_tests` | PASS | 0.8 |
| `sales_dispatch_as_of_tests` | PASS | 0.3 |
| `sales_dispatch_dock_pages_tests` | PASS | 0.4 |
| `sales_dispatch_tests` | PASS | 0.8 |
| `sales_invoice_aging_tests` | PASS | 1.0 |
| `sales_order_sent_total_tests` | PASS | 0.5 |
| `sales_orders_page_tests` | PASS | 1.1 |
| `sales_orders_tests` | PASS | 0.8 |
| `sales_order_totals_tests` | PASS | 0.4 |
| `sales_payments_returns_tests` | PASS | 1.0 |
| `sales_retry_cancel_trail_tests` | PASS | 0.6 |
| `sales_returnable_orders_page_tests` | PASS | 6.4 |
| `sales_revisions_price_floor_tests` | PASS | 1.2 |
| `sales_tests` | PASS | 0.5 |
| `security_sweep_hardening_tests` | PASS | 0.4 |
| `security_sweep_s1_fixes_tests` | PASS | 0.2 |
| `sell_damaged_decision_tests` | PASS | 0.9 |
| `sell_damaged_lines_tests` | PASS | 0.5 |
| `sell_damaged_shelf_tests` | PASS | 0.5 |
| `shipment_maintenance_tests` | PASS | 0.3 |
| `stock_adjustment_approval_tests` | PASS | 0.5 |
| `stock_set_aside_tests` | PASS | 0.4 |
| `suppliers_crm_tests` | PASS | 1.1 |
| `temp_password_db_block_tests` | PASS | 0.2 |
| `temp_password_reissue_tests` | PASS | 0.3 |
| `user_management_tests` | PASS | 0.3 |

**Reading a FAIL.** A suite that passes in CI and fails here means live differs from what the
repository builds, or something left rows behind in the shared database — a cancelled E2E run is the
usual one (`docs/pitfalls/infra-vps.md`). Each suite's full output is on the box in
`/opt/jahjah/sql-live/work/` and is **never published**: it can carry live values.

Stop: `touch /opt/jahjah/SQL_LIVE_OFF` · registry: `docs/runbooks/automations.md`
