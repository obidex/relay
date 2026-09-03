# GATE-1 ledger backfill — `20260902120000_sale_double_drain_backstop.sql`

<!-- index: GATE-1 backfill — the chunk-2 migration's SHA-256, put on the ledger retroactively -->

**Why this file exists.** The publish-before-apply guard (`D228`) was built in chunk 3 and made
honest in chunk 4. The last migration applied before it existed — chunk 2 T1's sale-double-drain
backstop — was approved and applied under the pasted-prompt flow, so its GATE-1 report predates the
`SHA256:` convention and its hash was never on the ledger.

This note puts it there. It is a **backfill of a migration that is already applied and already on
`main`**, not an approval of anything new.

**Migration:** `supabase/migrations/20260902120000_sale_double_drain_backstop.sql`
**Applied:** 2026-09-02, chunk 2 T1, merged as [#83](https://github.com/obidex/jahjah-internal/pull/83) (`42a9014`)
**Decision:** `D225`

SHA256: fd516faa54f16b3277c45599ae38919c90c04919d46d28b4ccda3539d30307ad

**What this does and does not mean.** It means the file's bytes are now on the relay, so the guard
recognises them — which is what made the guard's positive path testable at all without inventing a
fake migration. It does **not** re-authorize the migration: that approval happened at the time,
through the owner's chunk confirmation, and is recorded in `D225` and the chunk-2 reports.

**Verify it yourself:**

```
sha256sum supabase/migrations/20260902120000_sale_double_drain_backstop.sql
```
