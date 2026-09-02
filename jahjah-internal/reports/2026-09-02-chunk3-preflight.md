# Chunk 3 — T0 preflight

<!-- index: chunk 3 toolkit preflight — surface confirmed, ONE deviation on the plan check, no stop condition met -->

**Session:** chunk 3 — TOOLKIT (merge discipline and the SQL gate become mechanisms)
**Model:** Opus 5, effort `xhigh` (confirmed from `~/.claude/settings.json` → `modelSettings.claude-opus-5.effortLevel`)
**Tier:** 2 overall; T3 and T4 build security mechanisms and take the four-agent panel.
**When (UTC):** 2026-09-02

-----

## Verdict

**PROCEEDING**, with **one recorded deviation** on step 2 (the plan check). No STOP condition is met.
Everything else is confirmed clean.

-----

## 1. Surface

| Check | Result |
|---|---|
| `pwd` | `/root/jahjah-internal` — the ERP clone on the VPS work engine |
| tmux session | **`jahjah`** (windows: `claude OS workflow`, `bash`). The `web` session exists separately and was not touched. |
| `git status` | clean |
| HEAD at start | `83f37dd` — **one behind** `origin/main` |
| HEAD now | **`743b17e`** — `infra(vps): jahjah-web-docs + jahjah-web-backup (website P0) (#85)`, fast-forwarded, tree clean |
| `gh auth status` | OK, account `obidex`, scopes `gist, read:org, repo, workflow` |

The clone was one commit behind because #85 landed after this session's clone last fetched. A
`--ff-only` merge brought it to the commit the chunk plan names. Nothing was rebased or forced.

-----

## 2. DEVIATION — the plan check could not be run as written

The prompt's step 2 is `gh api /user --jq '.plan.name'` → must print `pro`. **It printed an empty
string.** Quoted verbatim, that is:

```
$ gh api /user --jq '.plan.name'

$
```

**That empty string is a token-scope artifact, not a statement about the plan.** Evidence:

1. The `plan` key is **absent** from the `/user` response, and so is every other field GitHub gates
   behind the `user` scope:

   ```
   plan: ABSENT
   private_gists: ABSENT
   total_private_repos: ABSENT
   owned_private_repos: ABSENT
   collaborators: ABSENT
   disk_usage: ABSENT
   two_factor_authentication: ABSENT
   ```

2. `X-Oauth-Scopes: gist, read:org, repo, workflow` — **no `user` / `read:user`**.

3. Step 6's billing call says so in as many words:

   ```
   $ gh api /users/obidex/settings/billing/actions --jq '{total_minutes_used, included_minutes}'
   {"message":"Not Found", ... "status":"404"}
   gh: This API operation needs the "user" scope. To request it, run:  gh auth refresh -h github.com -s user
   ```

   `gh auth refresh` is interactive and would modify the box's stored credential, so it was **not**
   run — an unattended session must not rewrite the auth it was handed.

**So the gate was answered by a different read-only probe that discriminates the plans exactly.**
Branch protection on a **private** repository is a paid-plan feature. On GitHub Free the endpoint
returns `403` with `Upgrade to GitHub Pro or make this repository public to enable this feature`. On a
paid plan it returns the ordinary "no protection configured" `404`:

```
$ gh api repos/obidex/jahjah-internal/branches/main/protection
{"message":"Branch not protected", ... "status":"404"}

$ gh api repos/obidex/jahjah-internal/branches/main/protection/required_status_checks
{"message":"Branch not protected", ... "status":"404"}
```

and the repository is confirmed `"private": true, "visibility": "private"`, owner type `User`.

**Conclusion: the account is on a paid plan and rulesets on this private repo are available.** This is
consistent with the chunk context (Pro bought 2026-09-02).

**Why this was a deviation and not a STOP.** The STOP condition is *"plan is not `pro`"*. The literal
command could not answer that question at all, and the question it stands for — *will a ruleset work
on this private repo* — is answered YES by the probe above. The residual risk is fully covered and
fails safe: T2's own STOP condition is *"ruleset API 403"*, so if this reading is wrong, the ruleset
POST stops the chunk there rather than doing anything unsafe.

**Follow-up for the owner (not blocking):** if the strategist wants the literal check to work in
future chunks, the box's `gh` token needs the `user` scope added — an interactive
`gh auth refresh -h github.com -s user`, which only the owner can run.

-----

## 3. Rulesets — none exist

```
$ gh api repos/obidex/jahjah-internal/rulesets
[]

$ gh api repos/obidex/jahjah-internal/rules/branches/main
[]
```

No ruleset targets `main`, and no legacy branch protection exists either. **T2 is clear to proceed.**

-----

## 4. Permission ask-rules — nothing blocks an unattended step

The repo's `.claude/settings.json` does carry `ask` rules that would matter — `Bash(rm:*)`,
`Bash(rmdir:*)`, `Bash(git clean:*)`, `Bash(git reset:*)`, `Bash(supabase db:*)`,
`Bash(supabase migration up:*)` — and T3/T4 need `rm` (kill-switch removal, sample cleanup).

**They were tested by execution rather than read from config**, which is the only trustworthy answer:

| Command | Result |
|---|---|
| `rm <scratch file>` | ran, no prompt |
| `systemctl daemon-reload` | ran, no prompt |
| `sha256sum` | present, ran |
| `flock` | present at `/usr/bin/flock` |
| `tmux list-windows -t jahjah` | ran |
| `git` / `gh` / `npm` | ran throughout this preflight |
| relay push | exercised by this report's own publication |

The session runs as **`root` (uid 0)**, so `systemctl` needs no `sudo` — which is how the P3 fleet
units were installed. **No ask-rule blocks an unattended step; no STOP.**

-----

## 5. Files read, and what each is needed for

| File | Why |
|---|---|
| `.github/workflows/ci.yml` | the seven job ids `ci-ok` must depend on |
| `docs/runbooks/testing.md` | the harness; **§2 is already stale** (see below) |
| `docs/runbooks/backup.md` | the exact apply form the GATE-1 hook must match |
| `docs/runbooks/automations.md` | the registry format and the relay conventions |
| `docs/pitfalls/infra-vps.md` | where the two new pitfall notes go |
| `docs/STRATEGIST.md` §1/§2 | the RELAY BLOCK format and the canon anchors |
| `/opt/jahjah/dispatcher/{dispatch.sh,run-job.sh}` + units | the conventions T4 must mirror |
| `/opt/jahjah/lib/jahjah-common.sh` | the shared half every new job must source |
| `infra/vps/**` | the repo path the box copies live under |
| `scripts/replay-check.sh` | where the grant audit is wired in |
| `~/.claude/settings.json` | `modelSettings` must survive the hook registration untouched |

### CI job inventory (what `ci-ok` will need)

Seven jobs, **no `if:` and no `paths:` conditions on any of them**:

| id | display name | needs |
|---|---|---|
| `checks` | Lint, type-check, unit tests | — |
| `secret-scan` | Secret scan (gitleaks) | — |
| `sast` | Static analysis (semgrep) | — |
| `types` | Schema type drift gate | — |
| `replay` | From-scratch DB replay | — |
| `sql` | SQL test suites (always rollback) | `checks` |
| `e2e` | Playwright E2E (CI-local build) | `checks`, `sql` |

`concurrency: ci-${{ github.ref }}` with `cancel-in-progress: true` — which is exactly why `ci-ok`
must treat `cancelled` as a failure and not as a pass.

### The exact apply forms the hook must match (`backup.md` §3)

A migration is applied **wrapped externally**, the file read verbatim:

```
begin;
<migration file verbatim>;
insert into supabase_migrations.schema_migrations (version, name) values ('<timestamp>','<name>');
commit;
```

against `psql -h db.<ref>.supabase.co -p 5432 -U postgres -d postgres`. So the hook must fire on a
`psql` invocation naming a path under `supabase/migrations/` via `-f`, `<` or `\i`, and on
`supabase db push` / `supabase migration up` — which is what T3 specifies.

### A stale doc found in passing

`docs/runbooks/testing.md` §2 says **"Three jobs"** and lists `checks`, `sql`, `e2e`. There have been
**seven** since the `types` and `replay` gates landed. PR-A adds an eighth, so that paragraph is
corrected in the same PR — it is the same root cause (the CI job list), not an unrelated tidy-up.

-----

## 6. Actions billing

```
$ gh api /users/obidex/settings/billing/actions --jq '{total_minutes_used, included_minutes}'
{"message":"Not Found","documentation_url":"https://docs.github.com/rest/billing/billing#get-github-actions-billing-for-a-user","status":"404"}
gh: This API operation needs the "user" scope.
```

**404, for the same missing scope as step 2.** Reported, not worked around. Note the repository is
private, so Actions minutes **are** metered against the account allowance — worth watching once
Dependabot starts filing weekly PRs (each one runs the full seven-job matrix). The cap of 3 open PRs
per ecosystem in A2 is the thing that bounds it.

-----

## 7. Node — report only, no change

| Where | Version | Source |
|---|---|---|
| CI | **22** | `ci.yml` → `env.NODE_VERSION: "22"`, used by all four `setup-node` steps |
| Vercel | **24.x** | project `jahjah-internal` (`prj_yPo68Y…`), `nodeVersion: "24.x"` |
| This box | **v22.23.2** (npm 10.9.8) | `node -v` |
| `package.json` | **no `engines` field** | read directly |
| `.nvmrc` / `.node-version` | **neither exists** | `ls` |
| `vercel.json` | **does not exist** | `ls` |

**So the pin CLAUDE.md §1 states is enforced in exactly one place — `ci.yml` — and Vercel builds two
majors ahead of it.** Nothing in the repo declares a Node version to Vercel, so the 24.x is a
dashboard setting only. This is a real drift between the stated pin and the production build, and it
is **left untouched** as instructed. It is the reason A2's Dependabot rider rule says never merge a
bump that needs a newer Node than the pinned 22.

-----

## What happens next

T1 (PR-A: `ci-ok`, Dependabot, the grant audit) → T2 (the ruleset) → T3 (the GATE-1 hook) →
T4 (the inbox lane, built and tested, **no real chunk started**) → T5 (PR-B and the canon close).

```
=== RELAY ===
HEAD: 743b17ef446fcdc89557f4ad0939bd814341fadc | tree: clean
CI: n/a — no commit pushed to jahjah-internal yet this chunk
DONE: T0 preflight complete; surface confirmed (VPS ERP clone, tmux `jahjah`, HEAD ff'd 83f37dd -> 743b17e); no ruleset exists on main; no ask-rule blocks an unattended step; CI job inventory, apply forms, Node matrix recorded
FILES: 1 relay report (jahjah-internal/reports/2026-09-02-chunk3-preflight.md); 0 repo files changed
FINDINGS/BLOCKERS: DEVIATION (not a stop) — `gh api /user --jq '.plan.name'` returns EMPTY because the box's gh token lacks the `user` scope, not because the plan is wrong; confirmed paid-plan another way (private-repo branch-protection endpoint returns "Branch not protected" 404, where Free returns a 403 naming the upgrade). Step 6 billing 404s for the same missing scope. Residual risk fails safe: a 403 on the T2 ruleset POST is already a STOP. Separately: docs/runbooks/testing.md §2 still says "Three jobs" when there are seven — corrected in PR-A. And the Node pin is enforced only in ci.yml (22) while Vercel builds on 24.x, with no engines/.nvmrc/vercel.json anywhere — reported, unchanged.
NEXT-NEEDED: none — proceeding to T1
=== END ===
```
