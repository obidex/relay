# P0 · Canon reset — progress (T0–T2 done, T3 in flight)

<!-- index: P0 progress — canon merged to master as PR #1, executor stood up, mirror live; two VPS automations built and proven from their timers -->

**Generated (UTC):** 2026-09-02T05:09:28Z · **Executor:** VPS `germany-vpn`, tmux `web`, Claude Code (Opus 5, xhigh)

## For the owner — one paragraph

The canon is on `master` and the site is up: **PR #1 merged, CI green, production deployed, the live
site answering 200.** Your two-minute `.gitignore` fix was the whole unblock. The website's rules now
publish themselves to a public address the strategist can read — that mirror went live two minutes
after the merge and is already showing the right commit. Two new machines are running on the VPS: one
copies the canon out every half hour, one takes a full backup of the catalogue every night at 02:30 and
keeps a week. **The backup's first run failed, on purpose-built checks, and that was the single most
useful thing that happened today** — it caught a bug that would have silently deleted good backups
forever. Fixed, re-proven, and written into the runbook so nobody rebuilds it. Nothing needs you right
now; one small thing needs ten seconds of your time when convenient, at the end.

-----

```
=== REPORT: P0 canon reset · progress ===
HEAD: master 939653a "docs: canon reset (P0) (#1)" | tree: clean | branch: master
PRs: jahjah-website #1 939653a MERGED (squash, branch deleted) — the canon, the agentic layer, CI,
       the reference generator, the two npm scripts
     jahjah-internal #85 OPEN — jahjah-web-docs + jahjah-web-backup
CI: jahjah-website master run 33593260375 green (build · verify · reference drift · gitleaks).
    PR run needed one fix: gitleaks 403 without `pull-requests: read`.
PROD: deployment 6215877078 sha 939653a state=success | live probes: 3/3 — / 200, /ar/ 200,
      /products/ 200
DONE:
  T0  three Actions secrets set from the VPS env file, names verified, no value printed or echoed
  T1  canon landed: exec bits, npm scripts, generated reference, two extractor fixes, three
      reviewer passes, PR #1 merged on green CI, production READY
  T2  executor stood up: /opt/jahjah/web on master, npm ci + build + verify clean from master,
      tmux `web` confirmed, .claude layer confirmed loading from the clone
  T3a jahjah-web-docs installed, armed, and PROVEN from the timer — mirrored 939653a, 6 files,
      0 absent, 2 minutes after the merge; public URLs 200
  T3b jahjah-web-backup installed, armed, PROVEN from the timer — 63167 bytes, 33 documents, 2s,
      keep-7 rotation, /root/backups/web mode 700, archive mode 600
  T3c both units enumerated in HEALTH-daily.md; registry rows written in the same change
DEVIATIONS:
  1. The plan said 26 files; the branch carries 15 and every file the plan names is present. The
     `.claude` layer is 5 files, not 17. Nothing is missing.
  2. EXPECTED_PAGES is 67, not the plan's 68 — see below.
  3. `.gitignore` is outside the plan's pre-authorized paths. Not reverted (tracking `.claude/**` is
     impossible without it); declared in the PR body and ratified as W092.
  4. The reviewer was invoked by content, not by agent name — this session's project root is /root,
     not the clone. Same definition, verbatim.
  5. The mirror was held off with its kill switch for 27 minutes before the merge (reason below).
FINDINGS/BLOCKERS: seven, all below. None open against master.
CANON: CLAUDE.md · docs/{STRATEGIST,STATE,ROADMAP,DECISIONS}.md · docs/archive/HISTORY.md ·
       docs/reference/site.md · scripts/{verify.sh,generate-reference.mjs} · .claude/** ·
       .github/workflows/ci.yml · .gitignore · package.json (scripts key) · W092–W095 appended
NEXT-NEEDED: one 10-second owner action (F13, below). Nothing blocking.
=== END ===
```

-----

## The backup bug — the most valuable thing in this chunk

`jahjah-web-backup`'s **first timer-driven run failed** and deleted a perfectly good 63 KB export,
reporting `no data.ndjson in the archive`. The archive was fine.

```
tar -tzf archive | grep -q '/data\.ndjson$'      →  exit 141
```

`grep -q` exits the moment it matches, closing the pipe under `tar`; `tar` dies of **SIGPIPE**; and the
`set -o pipefail` every job inherits from the shared library promotes that to the pipeline's status. So
`if ! …` reported **the exact opposite of the truth** — and the verify-then-rotate design, working as
intended, deleted the new archive rather than trusting it.

Left alone this would have failed **every night**, three nights running, and then disabled the job with
an alert saying the exports were corrupt. They would not have been.

It is invisible on small inputs: a toy reproduction with `cat` and three lines returns 0, because the
producer finishes before the reader exits. It only appears against the real artefact — which is exactly
why the runbook insists the first run comes from the timer against real data, and not from a hand-run.
Verified both ways against an actual export, fixed with `grep -c` (which reads to the end, so nothing is
signalled), re-proven from the timer, and registered as **trap 7** in the automations runbook — the
mirror image of trap 6, where `grep -c` was the bug.

## The reviewer, three passes

| Pass | Verdict | The one that mattered |
|---|---|---|
| 1 | 13 issues, **1 BLOCK** | `docs/STATE.md` carried `ssh root@<the box's public IP>` and the owner's Windows clone path — **in a file this chunk teaches a robot to publish to a world-readable repo every 30 minutes.** That IP is the exact value the fleet's own `jj_redact` scrubs from everything it publishes. |
| 2 | 14 issues, 0 BLOCK | Canon asserted numbers the build contradicts (21 products → 22; "101 sitemap URLs" → 67). The reference generator read the live filesystem, so a local ignored file changed its output and would have failed CI's drift gate on a file nobody edited. |
| 3 | 6 issues, 0 BLOCK | `STATE.md` §1 promised "the reset PR updates this line" for the merged hash — which that PR cannot do. After merge the hash would have been in no canon file at all. |

Two findings were **answered rather than obeyed**, both recorded instead of faked:

- The deny list cannot stop `Bash(grep:*)` reading `.env` while `Read(.env*)` is denied, and no
  enumerable list of readers closes it. Recorded as **W095** — *a guardrail, not a sandbox* — rather
  than pretending a fix. Where the gap **is** enumerable it was closed: nine push spellings that reach
  `master` are now denied, including bare `git push` on master and `git push origin <branch>:master`.
- Two passes claimed `.claude/agents/reviewer.md`'s `tools:` frontmatter was invalid. The documentation
  says that field takes permission-rule syntax; the file parses and the agent is listed. The third
  pass's new evidence — that its own run held unrestricted tools — does not apply, because these passes
  ran as a general-purpose agent carrying the definition's prompt. Registered as **F11** to be settled
  by observation, not by a fourth round of argument. The file is unchanged.

## Findings

| # | Finding | Status |
|---|---|---|
| 1 | The public IP and laptop path in a to-be-mirrored canon file | fixed before merge; the squash means neither ever reached `master`, and the public mirror is clean (swept) |
| 2 | `verify.sh` still FAILed after the pre-fix: `/ar/404/` is not under `/images/` | the 404 page's Arabic hreflang alternate has no route, so Vercel answers it with the **English** 404 body. A real defect; `src/**` is out of scope, so it is a **named** known-until-P1 WARN (not a glob — any other dead link still FAILs) and **F9** |
| 3 | The schema table in the generated reference was wrong | a lazy regex stopped at the first nested `defineField`, so `value` and `gallery` appeared twice, `variants` read as required and `slug` as optional. Brace-balanced walk; 27 source fields → 27 rows |
| 4 | The `COLOR_OPTIONS` guard passed on two **empty** lists | it read `value:` and `foo: {`; the source says `slug:`. It would have said IN SYNC however far the palettes diverged. Fixed, and it now says `EXTRACTOR MISS` rather than a false green |
| 5 | CI red on first run — gitleaks `403 Resource not accessible by integration` | the job declared `contents: read` only; `pull-requests: read` added. **A ROADMAP row had been narrowed minutes earlier on the unverified premise that the permissions were sufficient** — corrected; a follow-up closed on a guess is worse than an open one |
| 6 | `EXPECTED_PAGES` measured a different thing than its consumer | Astro counts 68 (including the `/admin` Studio); `verify.sh` deliberately excludes it and counts 67. Both right; only the second is what CI consumes |
| 7 | A paused mirror and a dead one looked identical on the health page | found while holding the mirror off deliberately. The job now publishes one `PAUSED` heartbeat when its kill switch appears, as the dispatch lane does |

**Why the mirror was held off.** `master`'s pre-reset `CLAUDE.md` contained the owner's Windows clone
path, and the relay's git history is permanent. The mirror was switched off from 04:40 until the merge
so its first published content was the reviewed canon — not the string this chunk had just removed from
the other canon file. Released at 05:05; the 05:07 slot mirrored `939653a`.

## Ten seconds of owner time — F13

Claude Code prints, in the executor clone:

```
Ignoring 30 permissions.allow entries from .claude/settings.json: this workspace has not been trusted.
```

The **deny** half is in force; the **allow** half is not, so every future session will prompt for
routine commands. The fix is a human trust decision and is deliberately not automated: run `claude`
once interactively in the executor clone and accept the dialog. The skills (`/verify`, `/ship`,
`/relay-report`) and the `reviewer` agent all resolve from the clone already — verified.

## Still to come

`jahjah-internal` **PR #85** (the two units) is open with CI running and an infra review in flight;
GATE 2 is pre-authorized there. Then the chunk-close PR sets `STATE.md` §1 to `939653a`, records both
squash hashes, and regenerates the reference. A final report follows.
