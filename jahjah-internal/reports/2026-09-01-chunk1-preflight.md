# Chunk 1 preflight — 2026-09-01

<!-- index: chunk-1 preflight — permissions clear, CI green, T1 premise CONFIRMED, T5 premise FALSE (partial dispatch) -->

**Session:** owner-approved five-task chunk `2026-09-01-chunk1`, executed unattended on the VPS work
engine. **Model:** Opus 5 (1M context), effort xhigh. **This report is the preflight only** — no
change of any kind has been made to the repository or the database at the time of writing.

## 1. Verdict

| | |
|---|---|
| **Permissions** | **CLEAR** — every command category this chunk needs executes without an interactive prompt. No stall risk. |
| **Repo** | `main` @ `8d980d3`, working tree **clean**. |
| **CI at HEAD** | **GREEN** — run `33454041050`, `success`, 8m33s, 2026-09-01T00:15:48Z. |
| **`currencies_anchor_immutable`** | exists on `public.currencies`, **`tgenabled = 'O'`** — plain ENABLED, bypassed by `session_replication_role = replica`. **T1 premise CONFIRMED.** |
| **Premise problems** | **ONE, and it is fatal to T5.** See §5. T1–T4 premises hold. |
| **Decision** | **PROCEED with T1 → T2 → T3 → T4.** **T5 will be a DEVIATION-STOP**, published as a BLOCKED report with the fallback design described and not applied. |

## 2. Box, lane and identity

Running on the VPS work engine (`germany-vpn`), which is the **primary** database lane — direct
`psql` and `pg_dump` both authenticate over IPv6 and were exercised successfully during this
preflight (`PostgreSQL 17.6 on aarch64`). The laptop fallback (`scripts/db-query.mjs`, Management
API) is **not** usable here and is not needed: `SUPABASE_ACCESS_TOKEN` is absent from this box's
`.env.local`, which holds only `NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_ANON_KEY` and
`SUPABASE_DB_PASSWORD`. Values were never read into the report; names only.

Git identity is the GitHub no-reply account, set **globally**, so a push from this box will be
attributed and Vercel will build it (`docs/pitfalls/infra-vps.md`):

```
obidex <144545793+obidex@users.noreply.github.com>
```

`gh` is authenticated as `obidex` with scopes `gist, read:org, repo, workflow`. The **`workflow`
scope is present**, which T3 and T4 both need — they modify `.github/workflows/ci.yml`, and a token
without that scope is rejected at push time with a message that looks nothing like a permission
problem.

## 3. Permission preflight — every category this chunk needs

Each was probed non-destructively. **None prompted; none stalled.**

| Category | Needed by | Probe | Result |
|---|---|---|---|
| `git` (status/log/branch/commit/push) | every task | `git status`, `git log` | ok |
| `gh` (PR create, checks, merge) | every task | `gh auth status`, `gh run list` | ok, `workflow` scope present |
| `psql` | T1, T5 premise, verification | `psql --version`, live query | ok, 17.11 client → 17.6 server |
| `pg_dump` | T1, T5 mandatory backup | `pg_dump --version` | ok, 17.11 |
| `docker` | T4 (ephemeral PG 17 replay), `replay-check.sh` | `docker --version` | ok, 29.7.2 |
| `npm` / `node` | build, lint, typecheck, tests, `npm run reference` | `--version` | ok, npm 10.9.8 / Node **22.23.2** (pinned major, correct) |
| Supabase type generation | T3 | `npx supabase` (devDependency) | available offline |
| file edits (Write/Edit/`sed`/heredoc) | all | exercised | ok |
| relay publish (`flock`, shared lib) | reports | `/opt/jahjah/lib/jahjah-common.sh` present | ok |

**Note on the repo allowlist.** `.claude/settings.json` does not list `psql`, `pg_dump`, `docker`,
`gh` or `bash` among its allowed Bash prefixes. That allowlist is **not** what is gating this
session — commands outside it ran without a prompt — so it is a documentation gap rather than an
operational blocker, and it is recorded here as a follow-up rather than edited mid-chunk. The
dispatcher lane's much tighter deny list (`run-job.sh`) is a **different** context and is untouched.

## 4. Live state — the T1 premise, read from the catalog

The trigger exists, is bound to all three verbs as `20260825120800` intended, and is in the WEAKER of
the two enabled states — the one T1 promotes. **The mechanism, the actor analysis and the exact
statements are deliberately NOT reproduced here:** this report is world-readable, and that class of
detail belongs only in `docs/pitfalls/money-fx.md`, which is excluded from the public docs mirror on
purpose (`docs/pitfalls/docs-mirror.md`). *(An earlier revision of this file did name it; trimmed on
2026-09-01. Git history is permanent, so the earlier revision cannot be recalled — recorded rather
than quietly dropped. Practical exposure is low: every path needs privileges no application-reachable
role holds.)*

The repo already anticipates the change in two places, which is a good sign the premise is sound:

- `docs/pitfalls/money-fx.md` already names the change and warns that the seed which establishes the
  anchor must be moved in the SAME change, or a re-run silently undoes it.
- `scripts/replay-check.sh`'s postlude already tolerates the post-change state **and carries a comment
  saying to tighten it once the migration lands and the seed is updated**.

## 5. Premise check, task by task

### T1 — promote the anchor trigger to ALWAYS — **PREMISE CONFIRMED**

Trigger exists, is plain `'O'`, and the approved statement applies as written. The from-scratch
replay path is understood: `20260825120800` creates the trigger (`'O'`), the new migration promotes
it (`'A'`), then `seed/05_currencies.sql` disables it to insert the anchor row and re-enables it —
so **the seed's re-enable is what decides the end state**, and it must become `enable always
trigger`. That is the whole alignment; nothing else changes.

### T2 — canon fixes — **PREMISE (a) CONFIRMED, (b) PARTLY DETERMINABLE**

**(a) The "connected by SKU" claim.** Present in four live canon files (`docs/archive/` excluded as
frozen): `docs/STRATEGIST.md` (header), `docs/STATE.md` (§1 sister-project row), `docs/ROADMAP.md`
(header and two body lines), and `docs/DECISIONS.md` (three historical entries). The correction will
be applied in place in the three living documents. **`docs/DECISIONS.md` is append-only**, so its
entries will be corrected by a new dated entry that supersedes them, not by editing history — the
same mechanism the chunk itself uses to close `D208`/`D209`/`D174`.

**(b) The routing-table model names.** The dispatcher under `/opt/jahjah` **specifies no model at
all** — `dispatch.sh` and `run-job.sh` invoke headless `claude -p` with no `--model` flag and no
`ANTHROPIC_MODEL` in the environment, so that lane inherits the account default and pins nothing.
The only model facts that *are* determinable are this session's own model (**Opus 5, 1M context**,
effort xhigh from `~/.claude/settings.json`) and the **panel seats**, which are pinned in
`.claude/agents/*.md` frontmatter: `bypass-reviewer` and `pg-semantics-reviewer` on `opus`,
`compliance-reviewer` and `test-validity-reviewer` on `sonnet`. The routing table will be updated
only where the source is certain, and the dispatcher's silence will be reported as a finding rather
than guessed at.

**Note for the record:** the chunk prompt states `MODEL: Opus 4.8`, but this session is actually
running **Opus 5 (1M context)**. Reported per the §17 pilot rule; the tier requirements are
unchanged by it.

### T3 — generated schema types — **PREMISE CONFIRMED**

`D208` is accurate: the Supabase clients carry no `Database` generic, so neither `npm run build` nor
`tsc --noEmit` can see a stale column string or a broken embed. Nothing blocks generating types and
adding a drift gate. App-wide adoption stays out of scope, as instructed.

### T4 — replay check as a CI gate — **PREMISE PLAUSIBLE, TO BE PROVEN BY RUNNING IT**

`scripts/replay-check.sh` exists, is Docker-backed, and Docker is available on this box. `D209`
records it passing 105/105. It will be run against current migration history **before** being wired
into CI; if it does not pass as-is, that is a DEVIATION-STOP for T4, not a fix-in-place.

### T5 — the D174 double-drain backstop — **PREMISE FALSE. This task will be BLOCKED.**

The premise to verify was: *at most one `sale_issue` movement per `source_sales_order_item_id` — by
writer-function design, in existing data, and in E2E fixtures.* It fails on the **first** of those
three, which is the one that matters.

**`public.dispatch_sales_order` writes one `sale_issue` per order line PER DISPATCH CALL, and
partial dispatch is a supported, shipped feature.** The function validates each line against *the
remaining quantity*, and step 10 deliberately leaves the order in `confirmed` when a backorder
remains. Two partial dispatches against the same order line therefore produce **two `sale_issue`
rows carrying the same `source_sales_order_item_id`** — which the approved unique index would reject
with a `23505`.

This is not theoretical. `supabase/tests/sales_dispatch_tests.sql` **TEST 2** is titled *"HAPPY PATH:
partial then completing dispatch"* and dispatches 4 units, asserts the order stays `confirmed`, then
dispatches the remaining 6 against **the same order item** and asserts it flips to `dispatched`.
Applying the approved SQL would turn a committed suite red and would break backorders in the live
product.

Adding `variant_id` to the index — the "(plus variant)" form `D174` itself floats — does **not**
help: both partial dispatches carry the same variant too.

Two further findings that belong in the eventual fix:

1. **The return-side index is sound.** `record_sales_return` inserts a fresh `sales_return_items`
   row per return line and links the movement to *that* new id, so one movement per return item
   holds by construction. It is the issue-side statement alone that is wrong — but the approved SQL
   is a single approved block, and applying half of it would be substitute SQL, which the stop rules
   forbid.
2. **The fallback design named in the chunk prompt is already shipped.**
   `public.record_stock_movement` already refuses `sale_issue` with **`JHI09`** and `sale_return`
   with **`JHI10`**, exactly as it refuses transfers with `JHI05`, and both are covered by existing
   suites. So the generic-writer half of `D174` is closed; the residual gap is only the **raw
   PostgREST `INSERT`** by a `manage_inventory` holder. The BLOCKED report will carry a corrected
   design keyed on the dispatch line rather than the order line.

Live data is clean either way — there are currently **zero** `sale_issue` and `sale_return` rows
(the ledger holds 3 `opening_balance`, 1 `stock_in`, 1 `adjustment`), and no duplicate source links
exist. The problem is entirely one of forward compatibility with a shipped feature.

## 6. What happens next

1. **T1 (+T2)** — mandatory `pg_dump` backup, migration, seed alignment, from-scratch replay proving
   `tgenabled = 'A'`, live apply, read-back from the catalog, `npm run reference`, four-agent panel,
   merge on green CI, report.
2. **T3**, then **T4**, each on its own branch, each merged only on a clean review and green CI.
3. **T5** — no SQL is applied. A BLOCKED report is published with the corrected design, and the
   chunk stops there, as the stop rules require. Everything merged before it stays merged.

Nothing in this chunk touches WireGuard, Docker port bindings, the kill switches, the automation
timers, or the website repository.
