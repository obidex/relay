# Chunk 8 install — the lane code is live on both lanes, both timers armed

<!-- index: chunk 8 install — the new chunk-lane code is live on both lanes, resolved config differs by one key, both timers armed and polling clean -->

**In one paragraph for the owner.** The new chunk-lane code is installed on the work engine and both
lanes are running it. The install window was about ninety seconds: both polls paused, the live copies
backed up, the files swapped atomically, the pause lifted. Both timers show a real next run and both
lanes have polled cleanly several times since. **Nothing about the website lane's configuration
changed** except one new setting that carries the website's own existing value — proven by printing
each lane's resolved configuration before and after and diffing it.

## What is live

| File | Installed as | Note |
|---|---|---|
| `web-dispatch.sh` | `0755` | the poll — detective control, chunk breaker, usage-cap gate, window recording |
| `run-chunk.sh` | `0755` | the runner — usage-cap classification, open-PR outcome, stream-json capture |
| `erp-dispatch.env` | `0644` | **byte-identical to what was there.** The retired flag only ever existed on an unmerged branch, so retiring it is the absence of a change |
| `README.md` | `0644` | the lane's plain-language note, rewritten |
| `health/health.sh` | `0755` | chunk failures, the usage-cap hold, and a last-24-hours dispatch table |

The shared library `jahjah-common.sh` was **not touched** — nothing here needed a new shared helper,
and it is sourced by ten other jobs.

## Both lanes' resolved configuration, before and after

`LANE_PRINT_CONFIG=1`, read from the installed copies, against the same command run before the
install. **One added line each, and it is the website lane's own value:**

```
--- WEBSITE lane ---            --- ERP lane ---
9a10                            9a10
> L_BLOCKED=chunk:blocked       > L_BLOCKED=chunk:blocked
```

Nothing else differs — not the repository, the worktree, the tmux session, the model, the effort, the
daily cap, the timeout, the runner path or the heartbeat interval. `chunk:blocked` already exists on
both repositories and already means "needs a person"; this adds a label *move*, not a label.

## The install itself

1. `bash -n` and `shellcheck --severity=error` over every touched script — **before** the install,
   never after, because a syntax error installed atomically is still installed.
2. Both polls paused (`ERP_DISPATCH_OFF`, `WEB_DISPATCH_OFF`). A running chunk would have been
   unaffected; there was none, and the website session had no `chunk-*` window.
3. Waited for both units to report `inactive` — a paused poll is not the same as a finished one.
4. Live copies backed up to `web-dispatch.pre-chunk8` and `health.sh.pre-chunk8`, which the sabotage
   checks then used as the "unfixed" side of every comparison.
5. Installed with `install -m`, which writes a temp file and renames — a running `bash` re-reads its
   script *by offset*, so an in-place edit would resume mid-run inside different text.
6. Pause lifted.

## Timers and polls after the install

Both timers show a real `NEXT`, not `-`:

```
jahjah-web-dispatch.timer   NEXT 00:40:00 UTC
jahjah-erp-dispatch.timer   NEXT 00:41:00 UTC
```

Six consecutive polls on each lane, all clean:

```
erp-dispatch  00:43 … 00:53   done in 0s — idle — nothing approved   (x6)
web-dispatch  00:44 … 00:54   done in 0s — idle — nothing approved   (x6)
```

## Lint scope, which is new

`bash -n` plus `shellcheck --severity=error` over **all 20** shell scripts in `infra/vps/**` and
`scripts/*.sh`: **clean, with nothing pre-existing to fix.** That is the exact body of the new
`shell-lint` CI job, run locally first. Until this chunk, nothing in CI looked at root-privileged
shell on a two-minute timer.

## Not done at this point

The sabotage table, the end-to-end database-lane smoke from the timer, the canon, the review panel,
the PR and GATE 2. The `.pre-chunk8` backups stay until the merge is confirmed.

```
=== RELAY ===
HEAD: d482475 + uncommitted work on branch chunk8-infra-lane | tree: dirty (work in progress)
CI: not run yet — no PR at this point. Locally: lint clean, tsc clean, 37/37 docs-mirror tests, bash -n + shellcheck --severity=error clean over all 20 shell scripts
DONE: The chunk-lane code is LIVE on both lanes. Installed atomically with install(1) inside a ~90s pause window, after bash -n and shellcheck on the exact bytes; live copies backed up to .pre-chunk8 first. Both lanes' resolved configuration, printed from the installed copies and diffed against the same command run before the install, differs by EXACTLY ONE added line — L_BLOCKED=chunk:blocked, the website lane's own value. The shared library was not touched. Both timers show a real NEXT and both lanes have polled clean six times since.
FILES: 5 installed — web-dispatch.sh, run-chunk.sh, erp-dispatch.env (byte-identical), README.md, health/health.sh.
FINDINGS/BLOCKERS: (1) web-dispatch/README.md was absent from infra/vps/README.md's drift loop and the box copy was STALE relative to the repo by exactly one section — a missed redeploy, not a divergence, and superseded by this install. Two more files were invisible to that loop (the ERP parameter file and the GATE-1 hook); all three are being added to it. (2) erp-dispatch.env needed no change at all: the flag this chunk retires was never merged, so retiring it is a zero-byte diff, which is worth stating rather than leaving as an apparent omission.
NEXT-NEEDED: none — the sabotage table, the database-lane smoke, the canon, the panel and GATE 2 follow in the same session.
=== END ===
```
