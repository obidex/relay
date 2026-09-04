# chunk 6 (resume) — PREFLIGHT + RESCUE

**Chunk 6 is running again, this time in an interactive session pasted onto the VPS work engine, which
is the only place that has a database lane.** The first attempt (issue #97, dispatched by the lane)
stopped exactly where it should have — a dispatched session has no DB lane, so it could not take the
mandatory `pg_dump` — and then died on the daily usage cap before it could publish anything. Its
report survived in the gitignored scratch directory and has now been published unchanged as the
first act of this session, so nothing that attempt learned is lost. The issue label has been moved
off `chunk:failed` (which described the dead process, not the work) to `chunk:running`.

Everything the plan asks the preflight to establish has been established **by running it**, not by
reading configuration: `psql` reads the live database, `pg_dump` writes a real dump, and every tool
the chunk needs answers. One difference from the written flow matters and is recorded here rather
than discovered at the gate: **this box has no `SUPABASE_ACCESS_TOKEN`, so the documented
`node scripts/db-query.mjs --migration` apply command cannot run here.** The chunk therefore applies
through the `backup.md` §3 wrapped form with `psql -1 -f`, with the `schema_migrations` insert in the
same transaction — which the GATE-1 hook checks identically, by content hash, because its check is
content-based and not path-based.

## T0.1 — RESCUE (done)

| | |
|---|---|
| Surviving report | `/root/jahjah-internal/.panel/chunk6-BLOCKED.md`, 44,286 bytes, written 2026-09-03 21:40 |
| Published as | `jahjah-internal/reports/2026-09-03-chunk6-BLOCKED.md` |
| URL | https://raw.githubusercontent.com/obidex/relay/main/jahjah-internal/reports/2026-09-03-chunk6-BLOCKED.md |
| Published verbatim | yes — no edit, its own IP/path scan had already passed, and the wrapper re-redacts on publish |
| Issue #97 | `chunk:failed` removed, `chunk:running` added; labels now `chunk:running`, `model:opus` |

## T0.2 — PREFLIGHT (all by execution)

| Check | Result |
|---|---|
| `pwd` | `/root/jahjah-internal` |
| tmux | session `jahjah` (`TMUX=/tmp/tmux-0/default`) |
| `git status` | clean (`.panel/` is gitignored) |
| `git fetch` → HEAD | `9c0ed84e3a50f9b5ae4bbd7c9245ad26b95cbd55` = `origin/main`; 9c0ed84 is an ancestor |
| Branch | `main` |

### DB lane — by execution, not by assumption

| Lane | Probe | Result |
|---|---|---|
| `psql` read-only | `select current_user, current_database(), version()` on `db.<ref>.supabase.co:5432` | **WORKS** — `postgres` / `postgres` / PostgreSQL **17.6** on aarch64 |
| `pg_dump` | schema-only dump of one table to a scratch path | **WORKS** — 6,579 bytes in 1.2 s, contains `CREATE TABLE`; probe file deleted |
| `node scripts/db-query.mjs` | ran against a trivial `select 1;` | **CANNOT RUN** — `Missing SUPABASE_ACCESS_TOKEN or NEXT_PUBLIC_SUPABASE_URL in env` |
| `.env.local` variable **names** | `NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_ANON_KEY`, `SUPABASE_DB_PASSWORD` | `SUPABASE_ACCESS_TOKEN` is **absent** — names only were read, never a value |
| Backup target | `/root/backups`, mode 700, holds 5 nightlies + 4 pre-migration dumps | ready |

**So the apply lane for this chunk is the one the prompt names as the fallback**, and it is the
documented `backup.md` §3 wrapped form rather than a hand-rolled variant:

```
psql -1 -f supabase/migrations/<file>.sql \
     -c "insert into supabase_migrations.schema_migrations (version, name) values (...);"
```

`-1` puts the file and the version row in **one** transaction (D219: never bare `psql -f`), and the
file is named by literal path so the GATE-1 hook can hash the exact bytes that will run.

### Ask-rules — by execution

| Tool | Result |
|---|---|
| `node` | v22.23.2 (Node 22 LTS, as pinned) |
| `psql` | 17.11 client |
| `pg_dump` | 17.11 |
| `gh` | 2.98.0, authenticated (read + issue edit both exercised) |
| `git` | fetch against `origin` works |
| `npm` | 10.9.8 |
| `/opt/jahjah/session/publish-report.sh` | works — it published the rescue report above |

### The GATE-1 hook, read before relying on it

Registered project-local in `.claude/settings.local.json` as a `PreToolUse` hook on Bash
(`/opt/jahjah/gate1/gate1-hook.sh`). It classifies the command, extracts every
`supabase/migrations/*.sql` path it names — including through variables, pipes and `bash -c` — hashes
the file, and allows only if `SHA256: <hash>` is already present on the relay's `origin/main` under
`jahjah-internal/reports/`. It fails closed on an opaque `-f` argument and on `supabase db push`.
**If it refuses after publication, this chunk stops and reports; it is never worked around.**

## What happens next, in order

1. T1 — re-verify against the **live catalog** the ten premises the first attempt confirmed, since a
   catalog can contradict a document but a document cannot contradict itself; then the two collateral
   breakages that attempt found (the `replay-check.sh` second arm and `activity_log_tests` 5a-ii),
   then measure the D225 residual and the D225(d) deadlock.
2. T2 — real `pg_dump` → publish the GATE-1 file with the final SQL, its diff against the approved
   text and its SHA-256 → apply → read the result back out of `pg_catalog`.
3. T3 — tests, the deadlock probe, the D229 audit hardening, `npm run reference`, and the four-agent
   panel on the full diff.
4. T4 — PR-F, canon (D232, D233, ROADMAP, STATE, two pitfalls files), merge, and the final report.

**No stop conditions have been hit.** The one deviation from the written flow — the apply command —
is a lane substitution the prompt anticipated and authorised, not a semantic change to anything.

```
=== RELAY ===
HEAD: 9c0ed84e3a50f9b5ae4bbd7c9245ad26b95cbd55 | tree: clean
CI: not run yet (no change made)
DONE: rescued and published the first attempt's surviving BLOCKED report; relabelled #97 chunk:failed -> chunk:running; preflight passed by execution
FILES: 0 changed (1 report published to the relay)
FINDINGS/BLOCKERS: no SUPABASE_ACCESS_TOKEN on this box, so `node scripts/db-query.mjs --migration` cannot run — the apply uses the backup.md §3 wrapped form with `psql -1 -f`, which the GATE-1 hook checks identically (content-based, not path-based)
NEXT-NEEDED: none — continuing unattended to T1
=== END ===
```
