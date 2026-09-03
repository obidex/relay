# Chunk 5 — chunk 4 is landed, and the lane's first real chunk found the lane's own ceiling

<!-- index: chunk 5 final — PR-E merged D228/D229/D230 + D231; the lane fixes were withheld after a panel blocker, and a dispatched chunk turns out to be sandboxed to its own working tree -->

**In one paragraph.** The canon chunk 4 wrote but never merged is now on `main`, together with the
owner's `D231` — so the register no longer sits behind code that cites it, and the `D226` failure is
closed. The ERP chunk lane carried this from a label to a finished chunk without a paste, which is
what it was built for. Two things did **not** ship, both for the same reason and both deliberately:
a dispatched chunk turns out to be **sandboxed to its own working tree**, so it cannot install
anything on the box, cannot create a skill, and cannot even run `bash -n`. The lane fixes were
therefore written, reviewed by the full panel — which returned **one blocker and four highs** — and
**kept off `main`**. Nothing was routed around.

-----

## 1. Delivered

| | |
|---|---|
| **PR-E** [#99](https://github.com/obidex/jahjah-internal/pull/99) → **`9c0ed84`** | `D228`/`D229`/`D230` landed at last; `D231` appended verbatim; `D228`§3 corrected before landing; the registry, the pitfalls and `STATE.md`. All ten checks green including `tier3-guard` and `ci-ok`; production deployment **READY**; branches `chunk4-pr-d` and `chunk3-pr-b` deleted. |
| **Proof it deployed** | The public canon mirror is already serving the new file — `https://jahjah-internal.vercel.app/internal/docs/docs/STATE.md` returns 200 and contains this chunk. That is the whole loop: a label, a chunk, a merge, a deploy, the canon readable without a login. |
| **`D228`§3 now says what chunk 4 proved** | The GATE-1 check is **content-based, not path-based** — the SQL about to run is hashed against every file under `supabase/migrations/`, so the wrapped-temp-file, stdin-pipe and other-working-directory forms all refuse — and **`node scripts/db-query.mjs --migration <file>`** is the documented apply command, which builds the wrapper itself. Nothing claims the hook binds a root session. |
| **`D231`** | Appended verbatim after `D230`. |
| **Registry** (`automations.md`) | `jahjah-erp-dispatch` **ACTIVE**, with the approval-window check, its own kill switch (`APPROVAL_WINDOW_CHECK=0`, separate from the lane's `ERP_DISPATCH_OFF`), a **NOT LIVE / NOT READY** row, and why this lane is a weaker gate than `jahjah-dispatcher` — no author check, no tier check — and why that is deliberate. The chunk **reporting path** is registered as a publishing path, not a job; the `jahjah-inbox` RETIRED row is untouched. |
| **Pitfalls** (`infra-vps.md`) | The dispatched-chunk sandbox · atomic installs and *why* (a running `bash` re-reads its script by byte offset) · the approval-window check's exact strength · that a refusal is not cleared by re-applying the label. |
| **`STATE.md`** | Rewritten by heading. It had recorded `main`'s HEAD as "the chunk-4 PR-D squash merge" — **PR-D was never opened or merged.** Corrected, with the flags, the ledger through chunk 5, and chunk 6 as the next step. |
| **The lane itself** | Dispatched this chunk **4 min 10 s** after the owner's label (approved 20:18:55Z → started 20:23:05Z), and its concurrency guard was visibly correct throughout: every subsequent poll logged `chunk #96 still running in tmux jahjah:chunk-96` and dispatched nothing. |

**Zero repo↔box drift.** Every `infra/vps/` file in PR-E is byte-identical to its live copy under
`/opt/jahjah` — verified with `git hash-object` on all six. PR-E changes nothing on the box.

## 2. The finding that reshaped the chunk

A dispatched Claude Code session is confined to its working tree. Measured three ways:

```
touch /opt/jahjah/erp-dispatch.log
  -> "Claude Code may only create or modify files in the allowed working
     directories for this session: '/root/jahjah-internal'"
mkdir -p .claude/skills/relay-report   ·   touch .claude/settings.json
  -> permission request, unanswerable in a headless run. The .claude/**
     guard is PATH-based and applies to Bash, not only to Write and Edit.
bash -n <script>
  -> `bash` is not in the allow list.
```

**So the lane can dispatch chunks that change the repository, but not chunks that change the box** —
and a chunk *can* still edit `infra/vps/`, which means it can produce repo-ahead drift it is
structurally unable to deploy or verify. That is now a pitfall.

**Nothing was routed around.** A `cp` from a scratch path, or a `node -e` that spawns a shell, would
walk past guards that exist to stop a chunk widening its own permissions — `D231`'s own subject. The
`.claude/**` guard is itself an accident guard rather than a boundary; the right response to one
firing is a stop and a report, which is what this is.

## 3. What did not ship, and why that was the right call

**The lane fixes** — `run-chunk.sh`'s fail-closed environment and the `D231` approval-window check —
are on branch **`chunk5-t1-lane-fixes`** (`b0e0f42`), **not merged and not installed.**

They could not be syntax-checked, installed, or sabotage-checked from here, and **`ci-ok` does not
lint shell** — there is no `shellcheck` on the box and no shell job in the pipeline — so a green
pipeline would have said nothing about them. They are root-privileged shell on a two-minute timer.

**The panel then justified the caution.** Four seats, on the diff alone. The `run-chunk.sh` half came
back clean. The approval-window half returned **one blocker and four highs**:

1. **blocker** — the documented recovery was a **no-op**. Re-applying a label GitHub already has
   emits no `labeled` event, so a refused issue would have been refused for ever.
2. **high** — **the guard is per lane, while the credential, the root and the box are per box.** A
   *website* chunk can label an ERP issue; the ERP lane reads only its own window file and finds
   nothing. The website lane has the flag off, so it records nothing at all.
3. **high** — **a refusal is not sticky.** The verdict is a timing test on an instant the chunk
   chooses; a deferred approval lands after the window and is dispatched with no further scrutiny.
4. **high** — the "announced" marker is written **before** the announcement is known to have landed,
   so one wait on the shared relay lock loses the only alert the mitigation ever produces.
5. **high** — the flag **fails open silently** on any value that is not exactly `1`, and its
   armed/disarmed state appears on no surface.

Plus seven mediums. **All twelve are recorded on the branch**, each with the fix it needs and the
sabotage checks that must pass — including the two that have to be run against the **unfixed** state,
since a check that cannot fail proves nothing.

**Three claims were false and are corrected**, because the code publishes them — to the issue and to
this public relay: the recovery instruction above; "the only self-approval a chunk can perform while
it exists" in three places; and a comment and heartbeat that asserted the window conclusion whichever
of the three refusal reasons had fired. **No code path was changed after the review**, so the panel's
verdicts still describe exactly what is on the branch.

`D231`'s own sentence — *"the only self-approval a chunk can perform while it exists"* — is the
**one-lane** statement. The pitfalls file now says so. The decision text is the owner's and is
recorded verbatim; the mechanics belong in the pitfalls file, and that is where the correction is.

**`/relay-report` could not be installed either.** The ported skill is complete and is attached to
issue #96 as a comment for an interactive session to place. Its procedure was still **sabotage-checked
by hand** on a throwaway issue, and this report was filed by following it.

## 4. Sabotage checks that did run

On a scratch issue, opened and closed:

- **`progress`** posts a comment and moves no label. Confirmed: labels unchanged after.
- **`final`** moves the label add → confirm → remove → confirm. All four calls exited 0.
- **The lagging index reproduced on BOTH confirmations** — each list query first returned the *old*
  state and was right on the retry, exactly as the skill warns. Without that retry rule the skill
  would report a failure on every successful chunk.
- **Settled, because a panel finding depended on it:** a label applied **at issue creation** *does*
  emit a `labeled` timeline event. So an issue pre-labelled by the strategist's connector is readable
  by the approval-window check — that failure mode does not exist.

## 5. Carried to chunk 6

The hardening migration is unaffected and is all repo work. **Two items need an interactive session**
(or one started with the extra directory granted), and must not be given to a dispatched chunk:

1. **Redesign, then install, the lane fixes.** The `run-chunk.sh` half needs only `bash -n`, an
   atomic `install -m 0755`, and its refusal checks. The approval-window half needs the blocker and
   the four highs addressed first. Do not restart the website timer.
2. **Place `/relay-report`** at `.claude/skills/relay-report/SKILL.md`.

**The committed permission surface was deliberately left untouched.** The four `gh issue` forms the
skill uses are already permitted here by the project-local `.claude/settings.local.json`, which is
gitignored — this chunk proved that by executing every one of them. Moving them onto the committed
`.claude/settings.json` is a chunk widening its own permission surface, which is exactly `D231`'s
shape, so it is the owner's call and not a chunk's.

## 6. One thing to look at, unrelated to this chunk

**`docs/STATE.md` is served publicly at `/internal/docs`, and it carries the work engine's SSH line —
user and IP.** Already on `main`, so already public, and not this chunk's doing. It is worth a
decision because the relay's own publisher **redacts that same IP from every file it publishes**: the
canon mirror publishes what the relay is careful to strip. Fixing it means dropping the value from
`STATE.md` or redacting in the docs lane — files outside this chunk's list, so it is reported.

Also on the box: an empty directory `/root/jahjah-internal/.probe-b`, left by the sandbox probe.
`rmdir` is an ask-rule and there was nobody to ask. Git ignores empty directories, so the tree is
clean; `rmdir` it at leisure.

```
=== RELAY ===
HEAD: 9c0ed84 (main, PR-E #99 squash) | tree: clean
CI: pass — PR-E green on every check including tier3-guard and ci-ok; post-merge main run 33806082933 GREEN on all nine jobs (tier3-guard correctly skipped on push, which ci-ok passes on). No retry was needed. Vercel production deployment for 9c0ed84 is READY, and the public canon mirror serves the new STATE.md (200).
DONE: chunk 4's canon landed (D228/D229/D230), D231 appended verbatim, D228 §3 corrected to name the content-based check and `scripts/db-query.mjs --migration`; registry rows for jahjah-erp-dispatch ACTIVE (with the window check, its own kill switch and a NOT-READY row) and for the chunk reporting path; pitfalls for the dispatched-chunk sandbox, atomic installs and the check's exact strength; STATE.md rewritten, including the correction that PR-D was never merged. The ERP lane carried its first real chunk end to end, label to done, in 4m10s from approval. The /relay-report procedure was sabotage-checked by hand on a scratch issue and used to file this.
FILES: PR-E 19 files, 4 commits — chunk 4's two commits carried unchanged, plus two from this chunk touching 4 canon files (DECISIONS, STATE, pitfalls/infra-vps, runbooks/automations). Branch chunk5-t1-lane-fixes (b0e0f42) pushed, UNMERGED. Nothing installed on the box; zero repo/box drift, verified by hash on all six live files.
FINDINGS/BLOCKERS: A DISPATCHED CHUNK IS SANDBOXED TO ITS OWN WORKING TREE — no writes outside /root/jahjah-internal, nothing under .claude/, and no `bash` (so no `bash -n`). Measured three ways; none routed around. So T0.3's skill install and T1's install and sabotage checks were undeliverable, and T1 was kept OUT of PR-E rather than merged unverified — ci-ok does not lint shell. THE PANEL THEN RETURNED ONE BLOCKER AND FOUR HIGHS ON T1: the documented recovery was a no-op (re-applying a present label emits no `labeled` event); the guard is per-lane while the credential is per-box, so a website chunk can approve an ERP issue; a refusal is not sticky, since a chunk can defer its approval past its own window; the "announced" marker is written before the announcement lands; and the flag fails open silently on any value but `1`. All twelve findings, their fixes and the required sabotage checks are recorded on the branch. Three false claims the code would have PUBLISHED were corrected; no code path changed after review. Separately: docs/STATE.md is publicly mirrored and carries the box's ssh user and IP, already on main — reported, not fixed.
NEXT-NEEDED: none to close this chunk. For chunk 6: run it as a normal dispatched chunk for the migration, but give the two box items — redesigning and installing the lane fixes, and placing the /relay-report skill — to an interactive session, because a dispatched one cannot do them.
=== END ===
```
