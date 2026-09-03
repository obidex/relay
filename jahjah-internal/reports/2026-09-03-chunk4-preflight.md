# Chunk 4 — T0 preflight

<!-- index: chunk 4 preflight — surface clean, label invariant holds, no stop condition; one premise in T3.2 is wrong and is named here -->

**Chunk:** 4 — WORKFLOW CONVERGENCE · **Model:** Opus 5, effort `xhigh` · **Tier:** 2 (T2/T3 take the panel)
**Date (UTC):** 2026-09-03

-----

## Verdict

**PROCEEDING.** No STOP condition is met. Two things differ from the prompt's assumptions and one
premise in T3.2 does not hold; all three are recorded below rather than worked around.

## 1. Surface

| Check | Result |
|---|---|
| `pwd` | `/root/jahjah-internal` |
| tmux session | **`jahjah`** (this session) |
| `git status` | clean |
| **`origin/main`** | **`2f2278b4`** — `infra(vps): jahjah-web-backup-check … (#92)` |
| `gh auth` | OK, `obidex`, scopes `gist, read:org, repo, workflow` |

**Deviation 1 — `main` has moved.** The prompt names `2c66949` (#91). **#92 landed after the prompt
was written**, so the base is `2f2278b4`. Nothing in this chunk depends on the older hash; PR-A
(`b4040fe`, ours) is still an ancestor. Recorded, not treated as a stop.

**Deviation 2 — no website chunk is running.** `tmux list-windows -t web` shows
`claude website workflow`, `bash`, `bash` — **no `chunk-<n>` window**, so the website lane is idle.
There *is* an interactive website session in that tmux; nothing in this chunk touches it,
`/opt/jahjah/web`, or the `jahjah-web-dispatch` unit's runtime state.

## 2. The external invariant the label gate rests on — HOLDS

```
$ gh api repos/obidex/jahjah-internal/collaborators --jq '.[].login'
obidex
$ gh api repos/obidex/jahjah-internal/invitations --jq 'length'
0
```

One collaborator, the owner; no pending invitations. **This is the whole strength of the label
gate** (`D230`): labelling needs at least Triage, and this box cannot enforce who holds it. Re-checked
every chunk, as the canon now requires.

## 3. Ask-rules — nothing blocks an unattended step

Tested by execution, not by reading config: `rm`, `systemctl`, `tmux`, `gh`, `git`, `sha256sum`,
`npm` all ran non-interactively as `root`. **`shellcheck` is NOT installed on this box** — every
script is checked with `bash -n` before and after instead, and that limitation is stated here rather
than left implied by T0.7.

## 4. Open pull requests

| PR | What | Plan |
|---|---|---|
| **#87** | actions group, 5 updates (incl. `gitleaks-action` v2 → v3) | **merge first, before PR-C**, so it needs no Tier-3 line |
| **#88** | npm minor/patch, 11 updates | merge after PR-C (T1b); first run of those versions under `sql`/`e2e` |
| **#89** | typescript 5.9.3 → **7.0.2** (major) | close with the standard comment |
| **#90** | eslint 9.39.4 → **10.9.1** (major) | close with the standard comment |

`gitleaks-action@v2` is node20 and GitHub drops node20 runners **2026-09-16**, so #87 is time-boxed
work, not tidying.

## 5. FINDING — T3.2's premise does not hold: there is no "apply helper"

T3.2 says to put the publish-before-apply check *"INSIDE the apply helper `backup.md` §3 uses (the one
that wraps the migration into `/tmp/replay.sql`)"*. **No such helper exists.** What actually exists:

- **`docs/runbooks/backup.md` §3 is a PROCEDURE, not a script.** It tells the operator to construct
  `begin;` + the file verbatim + a `schema_migrations` insert + `commit;` and run it. Nothing in the
  repo performs that for the live database.
- **`scripts/replay-check.sh` is the only thing that wraps into `/tmp/replay.sql`** — and it applies
  to a **throwaway Docker container**, not the live project. Putting a publish-check there would fail
  CI's replay of every migration ever written, which is the opposite of the intent.
- **`scripts/db-query.mjs <file.sql>` is the repo's one helper that executes a named SQL file against
  the LIVE database** (Management API). On this box the primary path is direct `psql`.

**So the guard will go where it can actually sit**, and the report at T3 will say so plainly:

1. **`scripts/db-query.mjs`** — refuse a file under `supabase/migrations/` whose SHA-256 is not
   published on the relay's `origin/main`. It fires only for migration paths, so CI's `sql` job
   (which runs `supabase/tests/*.sql`) and the E2E teardown are untouched, and CI has no relay clone
   to consult anyway.
2. **The project-local hook** — the raw `psql -f/</\i`, `supabase db push|migration up`,
   `node scripts/db-query.mjs <migration>`, `bash -c`, tilde and pipe forms the panel listed.
3. **`backup.md` §3 updated in the same PR**, since it is the single home of the apply flow.

Together those are the **accident guard** the owner asked for. Neither binds a root session holding
the database password, and `D228`'s wording already says exactly that — which is the point of the
rewrite.

## 6. `tier3-guard` — the pattern to mirror

Read from `obidex/jahjah-website`'s `ci.yml`. Its shape is a `git diff --name-only
origin/<base>...HEAD`, an anchored `grep -E` path alternation, then
`grep -Ec '^Tier-3: authorized by chunk .+'` over the PR body — **`grep -c`, never `grep -q` at the
end of a pipe** (the `pipefail`/SIGPIPE trap), failing closed with the offending paths printed. Its
`pull_request` `types:` carries `edited` so the guard re-runs when the line is added, with the
re-run cost accepted knowingly.

The ERP floor will be the paths in the prompt, anchored the same way. The website's own comment is
worth carrying over: **this is a floor, not a ceiling** — the judgment half of Tier 3 (`CLAUDE.md` §9)
is not path-expressible.

## 7. Node

`ci.yml` pins **22**; `package.json` has **no `engines` field**; there is no `.nvmrc` and no
`vercel.json`. The owner has set the Vercel project to 22.x, so C4's `"engines": { "node": "22.x" }`
makes the repo state the pin that the platform and CI already hold.

## 8. Files this chunk will touch

Read and confirmed present: `infra/vps/web-dispatch/{web-dispatch.sh,run-chunk.sh,README.md}` and its
units · `infra/vps/session/publish-report.sh` + README · `infra/vps/dispatcher/` (the Tier-1/2 issue
lane — **untouched**) · `docs/runbooks/automations.md` · `docs/runbooks/backup.md` ·
`scripts/replay-check.sh` · `scripts/db-query.mjs` · `.github/workflows/ci.yml` ·
`.github/dependabot.yml` · `.claude/settings.json` (ask-rules) and `.claude/settings.local.json`
(**exists, and `.gitignore` covers it**) · `~/.claude/settings.json` (the chunk-3 `hooks` key is
**still present**; `modelSettings` = `claude-opus-5: xhigh` and must survive) · branch
`chunk3-pr-b` · `docs/reference/app.md`.

**The lane parameterization will keep the scripts at `infra/vps/web-dispatch/` and
`/opt/jahjah/web-dispatch/`.** Moving them would change the website unit's `ExecStart`, and the
prompt forbids altering that unit's behaviour. The directory keeps the name of the first instance and
the README will say it is now shared code — a name is a smaller price than touching a live lane.

```
=== RELAY ===
HEAD: 2f2278b418... (origin/main, #92) | tree: clean
CI: n/a — nothing pushed yet this chunk
DONE: T0 preflight complete. Surface confirmed (ERP clone, tmux `jahjah`, clean, on main). Label-gate invariant re-checked and HOLDS (collaborators = obidex only, 0 invitations). Ask-rules clear by execution. Website lane idle — no chunk-<n> window. tier3-guard pattern read from the website repo. Open PRs #87-#90 triaged.
FILES: 1 relay report; 0 repo files changed
FINDINGS/BLOCKERS: (1) main is 2f2278b4, not the 2c66949 the prompt names — #92 landed after the prompt was written; no dependency on the old hash. (2) T3.2's premise is WRONG: there is no repo "apply helper" that wraps a migration for the live database. backup.md §3 is a procedure; the only thing wrapping into /tmp/replay.sql is replay-check.sh, which targets a throwaway container and must NOT carry this gate. The guard will go into scripts/db-query.mjs (the one helper that runs a named .sql against the live database), plus the project-local hook for the raw forms, with backup.md §3 updated in the same PR. (3) shellcheck is not installed on this box — bash -n only, stated rather than implied.
NEXT-NEEDED: none — proceeding to T1 (merge rider #87)
=== END ===
```
