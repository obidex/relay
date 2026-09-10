# Chunk 8 preflight — the lane code is written and green; nothing is installed yet

<!-- index: chunk 8 preflight — lane code written, bash -n + shellcheck clean, mirror lane closed with 9 sabotage-proven assertions; the box is untouched so far -->

**In one paragraph for the owner.** Chunk 8 is the interactive infra chunk. Everything it changes in
the repository is written and passing — the chunk lane's three new behaviours, the `/relay-report`
skill this project has been missing, the CI job that lints shell for the first time, and the change
that stops the public documentation mirror serving old session reports. **Nothing has been installed
on the work engine yet**, and neither chunk lane has been touched: that is the next step, and it
comes after this report is published, which is the rule. The five decisions in the plan were taken by
the owner and implemented as written; one premise in the plan turned out to be narrower than it read,
and it is named below rather than quietly worked around.

## Preflight invariants — all held

| Check | Result |
|---|---|
| `main` clean and at `origin/main` | yes — `d482475`, the Dependabot rider (#106) already merged and green, so GATE 2 for it was not needed |
| `main`'s `infra/vps/web-dispatch/*` byte-identical to branch base `bfd64f9` | **yes, all four files** — the branch's two commits are a clean delta |
| Collaborators on this repository | `obidex` only · invitations `0` |
| Collaborators on the website repository (the lane shares its code) | `obidex` only · invitations `0` |
| Repo↔box drift loop | **prints nothing** |
| Website chunk in flight | none — tmux `web` has no `chunk-*` window |
| Branch | `chunk8-infra-lane` from `origin/main` · issue **#107**, deliberately unlabelled |

**One drift note, outside the loop and reported rather than glossed.** `web-dispatch/README.md` is
not in `infra/vps/README.md`'s drift list, and the box's copy is **stale relative to the repo** by
exactly the section chunk 5 added. The box holds no content the repo lacks, so this is a missed
redeploy, not a divergence — the stop condition is about a file whose box copy has drifted *ahead*.
It is superseded by this chunk's own install, and the two files that were invisible to that loop
(`erp-dispatch.env`, `gate1/gate1-hook.sh` — both currently identical) are being added to it.

## What is written and green

| Area | State |
|---|---|
| `run-chunk.sh` | the branch's fail-closed environment as the base, plus usage-cap classification, the open-PR outcome, stream-json run capture, and a single label-move helper so the three outcomes cannot drift apart |
| `web-dispatch.sh` | the detective control (record, never refuse), the chunk breaker on its own counter, the usage-cap dispatch gate, window open-before-dispatch and close-at-`min(now, start+dur+120)`, 14-day pruning, and shape checks on the three values that reach a jq program, a REST path and arithmetic |
| `erp-dispatch.env` | **byte-identical, zero diff.** The retired flag only ever existed on the unmerged branch, so retiring it is the absence of a change |
| `health.sh` | chunk failures, the usage-cap hold, and a last-24-hours dispatch table |
| `/relay-report` | placed at `.claude/skills/relay-report/SKILL.md`, verbatim from issue #96's comment. **This report is being published through it** |
| Mirror | the `reports/` prefix lane removed from the registry, the tracing config and the manifest script |
| Riders | two no-op permission rules deleted; `shell-lint` added to CI and to `ci-ok`'s `needs` |

**Both lanes' resolved configuration, before and after, differs by exactly one line** — a new key
carrying the website lane's own default. That is the whole diff, and it is the owner's stated bound.

**Syntax and lint.** `bash -n` and `shellcheck --severity=error` over all 20 shell scripts in
`infra/vps/**` and `scripts/*.sh`: **clean, with nothing pre-existing to fix.** That is the exact body
of the new CI job, run locally first.

**The nine new mirror assertions were sabotage-checked against the unfixed source: all nine go red.**
They assert against the report files that are still on disk, so a test that passed for the wrong
reason would have shown up as a pass here.

## One premise corrected, not routed around

The plan's database-lane smoke test names `psql -f .panel/unpublished.sql` as the arm the GATE-1 hook
must refuse. **The hook is scoped to migration files by design** — it matches paths under
`supabase/migrations/` anywhere on the filesystem and deliberately ignores other SQL, because `D228`
defines the guard as hashing SQL against the migration set. A bare `.panel/unpublished.sql` is
therefore outside what the guard has ever claimed to cover, and a refusal there would be a scope it
does not have.

**So the smoke test uses a path the guard actually defines** — an unpublished file under a
`supabase/migrations/` path inside the scratch directory — which is the arm that proves what the plan
wanted proved: that the widened `psql` grant does not get past GATE-1. Both arms will be reported,
with the distinction stated, rather than one of them quietly dropped.

## Not done yet

The install, both lanes' pause window, the sabotage table, the end-to-end database-lane smoke from the
timer, the canon, the panel, the PR and the docker cleanup. The box is exactly as it was.

```
=== RELAY ===
HEAD: d482475 (branch chunk8-infra-lane, 0 commits ahead so far) | tree: dirty — work in progress, nothing committed yet
CI: not run — no commit and no PR yet. Locally: lint clean, tsc clean, 37/37 docs-mirror tests pass, bash -n + shellcheck --severity=error clean over all 20 shell scripts
DONE: (preflight only) Orientation complete and every preflight invariant held — collaborators obidex/0 on BOTH repositories, drift loop silent, no website chunk in flight, main clean at d482475, and main's four web-dispatch files byte-identical to branch base bfd64f9. Issue #107 opened with no labels. Lane code written: run-chunk.sh (fail-closed base + usage-cap classification + open-PR outcome + stream-json capture), web-dispatch.sh (detective control replacing the retired refusal, chunk breaker on its own counter, usage-cap dispatch gate, window open-before-dispatch, close at min(now,start+dur+120), 14-day prune, three shape checks), health.sh (chunk failures, cap hold, 24h dispatch table). /relay-report placed and IN USE for this report. Mirror reports/ lane removed from registry + tracing + manifest. Riders: two no-op permission rules deleted, shell-lint job added to ci-ok's needs.
FILES: 11 changed so far — infra/vps/web-dispatch/{web-dispatch.sh,run-chunk.sh,README.md}, infra/vps/health/health.sh, .claude/skills/relay-report/SKILL.md, .claude/settings.json, .github/workflows/ci.yml, src/lib/docs/{registry.ts,server.ts,registry.test.ts,routes.test.ts}, next.config.ts, scripts/generate-docs-manifest.mjs. erp-dispatch.env is byte-identical by design.
FINDINGS/BLOCKERS: (1) PREMISE CORRECTED, NOT WORKED AROUND — the plan's DB-lane smoke names a non-migration path as the file GATE-1 must refuse. The guard is scoped to migration-file paths by design (D228 hashes SQL against the migration set), so that path is outside what it has ever claimed; the smoke will use a path the guard actually defines and report BOTH arms with the distinction stated. (2) web-dispatch/README.md is absent from the drift loop and the box copy is STALE relative to the repo by exactly chunk 5's added section — a missed redeploy, not a divergence; superseded by this chunk's install, and the two files invisible to that loop are being added to it. (3) The nine new mirror assertions were run against the UNFIXED source and all nine fail, so none of them can pass for the wrong reason. (4) Both lanes' resolved configuration differs before/after by exactly one new key at the website default.
NEXT-NEEDED: none — the five decisions are the owner's and are being implemented as written. The install, the sabotage table, the DB-lane smoke, the canon, the panel and GATE 2 follow.
=== END ===
