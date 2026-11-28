# SQL suites against LIVE — nightly

<!-- index: nightly SQL suites vs LIVE — FAIL 2/134 -->

**Generated (UTC):** 2026-11-28T03:15:00Z
**Verdict:** **FAIL** — 2 of 134 suites did not pass: `permission_system_tests (line 694, P0001)` `seller_book_price_read_tests (line 87, P0001)` 
**Suites from:** `obidex/jahjah-internal` `main` at `43948eb`
**Migrations:** in step — all 230 migrations on main are applied on live, and live has none main lacks (newest `20261128260000`)
**Browser runs on the shared database:** none open
**Ran for:** 224s

This is the nightly run of the always-rollback SQL test suites against the **live** database, from
the work engine. Pull-request CI runs the same suites in a throwaway database built from the
repository; this run is the one that sees live. **Stale by more than ~26 hours = this job is not
running** (or is switched off: `/opt/jahjah/SQL_LIVE_OFF`).

| Suite | Result | Seconds |
|---|---|---|
| `activity_log_record_history_tests` | PASS | 0.9 |
| `activity_log_tests` | PASS | 0.7 |
| `arabic_cut_tests` | PASS | 0.7 |
| `assembly_breakage_key_tests` | PASS | 1.0 |
| `assembly_close_return_key_tests` | PASS | 0.6 |
| `assembly_costing_tests` | PASS | 2.0 |
| `assembly_issue_more_parts_tests` | PASS | 1.4 |
| `assembly_items_cost_tests` | PASS | 0.8 |
| `assembly_location_read_tests` | PASS | 0.3 |
| `assembly_making_price_atomic_tests` | PASS | 0.6 |
| `assembly_schema_tests` | PASS | 1.1 |
| `book_price_without_price_key_tests` | PASS | 1.8 |
| `business_settings_keys_tests` | PASS | 0.3 |
| `cash_drawer_integrity_tests` | PASS | 1.9 |
| `cash_rate_today_tests` | PASS | 0.3 |
| `cash_refund_state_tests` | PASS | 1.2 |
| `cash_sessions_cash_sale_cheques_tests` | PASS | 0.9 |
| `catalog_identity_tests` | PASS | 1.2 |
| `catalog_supplier_tests` | PASS | 0.8 |
| `confirm_sales_order_draft_tests` | PASS | 0.7 |
| `credit_collections_returns_tests` | PASS | 1.2 |
| `credit_gate_and_holds_tests` | PASS | 1.5 |
| `customer_price_tier_gate_tests` | PASS | 0.2 |
| `d252_daily_syp_rate_tests` | PASS | 0.3 |
| `damage_grade_history_tests` | PASS | 0.4 |
| `data_status_events_tests` | PASS | 0.2 |
| `dispatch_driver_capacity_tests` | PASS | 0.5 |
| `dispatch_left_before_count_tests` | PASS | 0.8 |
| `draft_save_rejudges_lines_tests` | PASS | 0.8 |
| `fx_cut_tests` | PASS | 0.8 |
| `imports_costs_tests` | PASS | 0.2 |
| `imports_landed_cost_tests` | PASS | 0.4 |
| `imports_milestones_tests` | PASS | 0.3 |
| `imports_shipments_tests` | PASS | 0.3 |
| `imports_value_lock_supplier_freeze_tests` | PASS | 0.4 |
| `inventory_receipt_transfer_tests` | PASS | 0.7 |
| `inventory_reorder_tests` | PASS | 0.3 |
| `inventory_stock_tests` | PASS | 0.3 |
| `landed_cost_real_charges_tests` | PASS | 0.3 |
| `landed_value_true_up_tests` | PASS | 0.9 |
| `launch_import_items_customers_tests` | PASS | 0.7 |
| `launch_import_opening_stock_tests` | PASS | 0.5 |
| `launch_import_reversal_tests` | PASS | 0.8 |
| `launch_import_split_and_twins_tests` | PASS | 0.4 |
| `launch_import_tests` | PASS | 1.4 |
| `low_stock_request_variants_tests` | PASS | 0.2 |
| `maintenance_locations_tests` | PASS | 0.8 |
| `member_roles_one_transaction_tests` | PASS | 0.3 |
| `members_page_tests` | PASS | 0.3 |
| `off_book_price_setting_key_tests` | PASS | 0.5 |
| `opening_stock_reversal_tests` | PASS | 0.7 |
| `operation_replays_tests` | PASS | 0.6 |
| `payments_cheques_rules_tests` | PASS | 1.4 |
| `payment_terms_fixed_tests` | PASS | 0.3 |
| `payment_unapply_one_transaction_tests` | PASS | 0.4 |
| `permission_descriptions_tests` | PASS | 0.1 |
| `permission_system_tests` | **FAIL** (psql exit 3; line 694, P0001) | 0.3 |
| `platform_access_fixes_tests` | PASS | 0.3 |
| `platform_minor_fixes_tests` | PASS | 0.2 |
| `po_add_payment_lock_tests` | PASS | 0.4 |
| `po_gates_tests` | PASS | 0.5 |
| `policy_permission_once_tests` | PASS | 0.3 |
| `po_payments_planned_rule_tests` | PASS | 0.4 |
| `po_save_lines_by_id_tests` | PASS | 0.9 |
| `po_schedule_one_transaction_tests` | PASS | 0.4 |
| `po_shipment_bridge_tests` | PASS | 0.3 |
| `procurement_tests` | PASS | 0.5 |
| `product_images_tests` | PASS | 0.2 |
| `purchase_approvers_read_and_decide_tests` | PASS | 1.2 |
| `purchase_order_3b_tests` | PASS | 0.3 |
| `purchase_order_payment_fail_closed_tests` | PASS | 0.3 |
| `purchase_order_payments_tests` | PASS | 0.3 |
| `purchase_order_payment_summaries_tests` | PASS | 0.8 |
| `purchase_order_variant_plan_tests` | PASS | 0.3 |
| `receipt_applies_shown_orders_tests` | PASS | 0.8 |
| `receipt_reversal_tests` | PASS | 0.5 |
| `receipt_short_arrival_tests` | PASS | 0.5 |
| `receivables_paging_tests` | PASS | 9.1 |
| `receive_shipment_atomic_tests` | PASS | 0.5 |
| `receiving_pieces_per_unit_tests` | PASS | 0.7 |
| `receiving_po_open_quantity_tests` | PASS | 1.4 |
| `receiving_reconciliation_tests` | PASS | 0.8 |
| `receiving_verifier_fixes_tests` | PASS | 0.8 |
| `record_member_names_tests` | PASS | 0.4 |
| `reference_data_tests` | PASS | 0.3 |
| `reports_tests` | PASS | 0.5 |
| `resolve_sale_prices_tests` | PASS | 3.4 |
| `return_maintenance_tests` | PASS | 1.1 |
| `return_refunds_tests` | PASS | 2.0 |
| `role_keys_you_hold_tests` | PASS | 0.2 |
| `sale_price_book_page_tests` | PASS | 0.5 |
| `sale_price_ranges_discounts_tests` | PASS | 1.1 |
| `sale_price_ranges_tables_tests` | PASS | 0.3 |
| `sale_price_resolver_hidden_tier_tests` | PASS | 0.8 |
| `sales_credit_marks_trail_tests` | PASS | 1.0 |
| `sales_dispatch_as_of_tests` | PASS | 0.4 |
| `sales_dispatch_dock_pages_tests` | PASS | 0.4 |
| `sales_dispatch_tests` | PASS | 0.7 |
| `sales_invoice_aging_tests` | PASS | 1.0 |
| `sales_order_sent_total_tests` | PASS | 0.5 |
| `sales_orders_page_tests` | PASS | 0.7 |
| `sales_orders_tests` | PASS | 0.7 |
| `sales_order_totals_tests` | PASS | 0.4 |
| `sales_payments_returns_tests` | PASS | 1.1 |
| `sales_retry_cancel_trail_tests` | PASS | 0.6 |
| `sales_returnable_orders_page_tests` | PASS | 1.4 |
| `sales_revisions_price_floor_tests` | PASS | 1.2 |
| `sales_tests` | PASS | 0.4 |
| `sealed_receipt_markers_tests` | PASS | 0.5 |
| `security_sweep_hardening_tests` | PASS | 0.4 |
| `security_sweep_s1_fixes_tests` | PASS | 0.1 |
| `sell_damaged_decision_tests` | PASS | 0.8 |
| `sell_damaged_lines_tests` | PASS | 0.5 |
| `sell_damaged_shelf_tests` | PASS | 0.5 |
| `seller_book_price_read_tests` | **FAIL** (psql exit 3; line 87, P0001) | 0.1 |
| `sequence_access_tests` | PASS | 0.2 |
| `service_role_provisioning_grants_tests` | PASS | 0.2 |
| `shipment_document_path_tests` | PASS | 0.3 |
| `shipment_maintenance_tests` | PASS | 0.3 |
| `stock_adjustment_approval_tests` | PASS | 0.6 |
| `stock_count_counter_candidates_tests` | PASS | 0.2 |
| `stock_count_fixes_tests` | PASS | 0.5 |
| `stock_count_tests` | PASS | 1.4 |
| `stock_reversal_zero_basis_tests` | PASS | 0.6 |
| `stock_set_aside_tests` | PASS | 0.4 |
| `stock_zero_basis_tests` | PASS | 0.2 |
| `supplier_bill_activity_trail_tests` | PASS | 0.7 |
| `supplier_bills_tests` | PASS | 121.6 |
| `supplier_iban_invoice_key_tests` | PASS | 0.7 |
| `suppliers_crm_tests` | PASS | 0.9 |
| `sweep_guards_tests` | PASS | 0.8 |
| `temp_password_db_block_tests` | PASS | 0.3 |
| `temp_password_reissue_tests` | PASS | 0.3 |
| `user_management_tests` | PASS | 0.4 |

**Reading a FAIL.** A suite that passes in CI and fails here means live differs from what the
repository builds, or something left rows behind in the shared database — a cancelled E2E run is the
usual one (`docs/pitfalls/infra-vps.md`). Each suite's full output is on the box in
`/opt/jahjah/sql-live/work/` and is **never published**: it can carry live values.

Stop: `touch /opt/jahjah/SQL_LIVE_OFF` · registry: `docs/runbooks/automations.md`
