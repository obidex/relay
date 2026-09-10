**Chunk 8 smoke — the dispatched-chunk database lane: GRANTED BUT NOT USABLE HEADLESSLY (ii). The GATE-1 guard HELD.**

A dispatched chunk on the box can run `psql` and `pg_dump` as bare commands, and they reach the Supabase host, but it cannot authenticate. The box has no `~/.pgpass`, and the only way to supply the password — a `PGPASSWORD=` assignment in front of the command, filled by a command substitution from the gitignored env file — is refused by the permission engine before it runs, because command substitution cannot be statically analysed against the `Bash(psql:*)` prefix rule. So the grant exists, and every command it permits fails authentication; the form that would authenticate is not permitted. No migration, no database write, no commit, no push and no branch; nothing outside `.panel/` was edited. The GATE-1 accident guard refused `psql -f` on an unpublished migration file, as it must. Arm 4 used the bare form on purpose, so a guard failure could not have executed anything: no credential was supplied, and the file holds only `select 2;`.

| Arm | Permitted? | Outcome |
|---|---|---|
| 1 — `psql --version` / `pg_dump --version` | Yes / Yes | `psql (PostgreSQL) 17.11` · `pg_dump (PostgreSQL) 17.11` (Ubuntu 17.11-1.pgdg24.04+2) |
| 2a — bare `psql -c 'select 1 as ok'` | Yes | Reached the server; `fe_sendauth: no password supplied`. This is the expected success for the arm: the command was permitted and connected. |
| 2b — `PGSSLMODE=require PGPASSWORD="$(grep … \| cut …)" psql -c 'select 1 as ok'` | **No** | Refused before execution: `Contains shell syntax (string) that cannot be statically analyzed`. No row returned. |
| 3 — bare `pg_dump --schema-only -t public.branches \| head -3` | Yes | Reached the server; `fe_sendauth: no password supplied`. No DDL returned, so there are no lines to show. |
| 4 — bare `psql -f .panel/supabase/migrations/29990101000000_chunk8_unpublished_smoke.sql` | **No — GATE-1 refused** | Blocked by the PreToolUse hook before execution (verbatim below). |

GATE-1 refusal, verbatim:

```
PreToolUse:Bash hook error: [/opt/jahjah/gate1/gate1-hook.sh]: GATE-1 guard: .panel/supabase/migrations/29990101000000_chunk8_unpublished_smoke.sql sha256 ac4396cdee0295db27f816dc31134189999d0071663e618f4957bc23edb584d7 is not published on relay origin/main — publish the GATE-1 report carrying "SHA256: ac4396cdee0295db27f816dc31134189999d0071663e618f4957bc23edb584d7" and push it first (D223/D228)
```

Not measured, on purpose: whether an assignment-prefixed form WITHOUT command substitution is permitted. Testing it would mean putting the credential literally on the command line and into the transcript, which this chunk forbids. The guard was measured only for the `-f` form.

```
=== RELAY ===
HEAD: 752daf0 (branch chunk8-infra-lane) | tree: clean (report scratch in gitignored .panel/)
CI: none — read-only smoke, no commit, no push
DONE: ARM 1 psql/pg_dump granted (17.11) · ARM 2a bare psql permitted, reached server, no password supplied · ARM 2b PGPASSWORD-prefixed form REFUSED ("Contains shell syntax (string) that cannot be statically analyzed") · ARM 3 bare pg_dump permitted, reached server, no password supplied · ARM 4 GATE-1 REFUSED the unpublished -f file (guard held) · VERDICT (ii) granted but not usable headlessly
FILES: 0 repo files changed; scratch .panel/relay-report.md only (gitignored)
FINDINGS/BLOCKERS: (1) The DB lane grant is not usable by a dispatched chunk: every permitted form lacks a credential, and the one form that carries one is refused by static analysis. (2) Invoking the relay-report skill through the Skill tool returned only "Execute skill: relay-report" with no instructions; the report followed .claude/skills/relay-report/SKILL.md read from disk instead. (3) Unmeasured: the assignment-prefix form without command substitution, and any GATE-1 coverage beyond psql -f.
NEXT-NEEDED: strategist decision on the lane's credential route — A) a mode-600 ~/.pgpass (or PGPASSFILE) for the dispatch user so the bare, granted form authenticates, but only after confirming what GATE-1 covers besides -f, because a working credential makes -c writes possible too; or B) keep DB work in the interactive tmux lane (D233) and drop the dispatched grant. Recommendation: B, unless a dispatched chunk genuinely needs read-only DB access.
=== END ===
```
