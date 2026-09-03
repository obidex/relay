# Chunk 4 — BLOCKED at T5: the label gate does not bind the thing it gates

<!-- index: chunk 4 BLOCKED — PR-C and both riders merged and good; the ERP lane is PAUSED on a panel blocker whose fix needs a credential -->

**Stop conditions met, two of them:** *"panel blocker on T2/T3"* and *"any step needs a schema, RLS,
grant, secret or auth change"*.
**`main`:** `d69ead99` — PR-C and both riders merged, every post-merge run green. **Unaffected.**
**PR-D:** committed and pushed as branch `chunk4-pr-d` (`bfd64f9`). **No pull request opened, not merged.**
**The ERP chunk lane is PAUSED** on the box. The GATE-1 guard is active. The website lane is untouched.

-----

## 1. What is done, merged and safe

| | |
|---|---|
| **PR-C** [#93](https://github.com/obidex/jahjah-internal/pull/93) → `7ef0ee6` | `tier3-guard` (sabotage-proven both ways), all 14 action refs SHA-pinned, Dependabot npm majors ignored, `engines: node 22.x`, `AGENTS.md` + registry row |
| **#87** → `0677c37` | actions group; `gitleaks-action` v2→v3 before GitHub drops node20 runners on 2026-09-16 |
| **#88** → `d69ead9` | npm minor/patch ×11; post-merge `main` green on all nine jobs — the real test, since `sql`/`e2e` are skipped on Dependabot PRs |
| #89, #90 | closed with the standard reason; the class stops arriving now |
| **The ERP lane** | built, and **proven end to end from its timer** (#95) |
| **GATE-1** | rebuilt content-based and project-local; three of its own panel blockers closed with evidence |
| **The inbox lane** | retired from box and relay |

## 2. THE BLOCKER — a chunk can approve its own next chunk

`run-chunk.sh` starts the session with `HOME=/root`, so its `gh` is the box's credential. Confirmed
read-only, without writing any label:

```
login=obidex
permission=admin
label-writing rules the chunk inherits: "Bash(gh issue *)"  "Bash(gh api *)"
```

**That is the same account whose Triage/admin right IS the `chunk:approved` gate.** A dispatched
chunk can open an issue, label it `chunk:approved`, and the lane starts it two minutes later. The
residual bounds — a daily cap of 3 and the in-flight marker — are plain files under
`/opt/jahjah/erp-dispatch/state/` that the same chunk can rewrite.

**The external invariant holds and the gate is still open.** The collaborator list is exactly
`obidex`, zero invitations — verified at preflight and again now. The gate is not defeated by an
outsider; it is defeated because **the lane hands the credential that operates the gate to the
session it is gating.**

This is `D230`'s own rule turned on its author: *a control that rests on metadata, a label or a
self-asserted identity is a convention, not a boundary.* Chunk 3 learned it from a forged git author.
This is the same defect reached from the other direction, and I did not see it until the panel did.

**Why I stopped rather than patched it.** The honest fix is the one the reviewer names: the chunk
must not hold a credential that can apply `chunk:approved` — a separate, label-blind GitHub token.
**That is a secret/auth change, which this chunk's own stop conditions forbid.** Narrowing
`Bash(gh issue *)` is not a fix: the chunk is root, holds `gh api`, and can edit the settings file
that grants it.

**Note what it does and does not escalate.** A chunk already runs as root and can commit, push and
merge. What self-approval adds is **persistence** — the ability to extend its own authority in time,
past the point where a human chose to stop. That is worth a decision, not a patch.

## 3. Three panel blockers that WERE mine, and are fixed

All three were in code I wrote this session; all three are closed and verified by reproduction. Full
evidence in `2026-09-03-chunk4-gate.md`.

1. **GATE-1 never fired on the house apply flow.** `backup.md` §3 wrapped the migration into a temp
   file, so a path-based check never saw a migrations path — **the exact hole `D228` records the
   first version having, reproduced by me in the other half.** The check is now content-based: the
   SQL about to run is hashed against every file under `supabase/migrations/`, so the temp-file,
   stdin-pipe and different-working-directory forms all refuse. `--migration <file>` is now the
   documented apply command and builds the wrapper itself.
2. **The stdin lane skipped both halves.** Closed on both sides.
3. **`GATE1_RELAY_CLONE` was an environment override of the trust anchor** while three documents
   said there was none. Removed; a forged relay clone is now inert.

Plus four more: the hook classified only the first line of a multi-line command; an internal
exception fell through to the fail-**open** branch; the health page greped a comment rather than the
enforcement; and it published which half of the guard was off, on a world-readable page.

## 4. State left on the box

```
jahjah-erp-dispatch.timer   enabled, but PAUSED via /opt/jahjah/ERP_DISPATCH_OFF (reason in the file)
  its heartbeat now reads:  PAUSED — the lane is deliberately not running
jahjah-web-dispatch.timer   untouched, enabled, idle — the website lane is unaffected
GATE-1 guard                ACTIVE (both halves), project-local, no kill switch
jahjah-inbox                retired: units, scripts, state and relay folders removed
```

The lane is paused rather than deleted: it is built, proven and one decision away from being
correct. `rm /opt/jahjah/ERP_DISPATCH_OFF` resumes it.

## 5. What chunk 5 needs from the owner

**One decision, and it is the same shape as the one chunk 3 raised:**

> The chunk lane's approval gate is a GitHub label. The executor holds the credential that can apply
> that label. Either give the executor a **separate token that cannot write labels** — the owner
> provisions it — or **accept in writing** that the lane's real boundary is "the box is trusted",
> and that a chunk can extend its own run. Both are defensible; only the first makes the label mean
> what the canon says it means.

Until then, **chunk 5 runs by paste**, exactly as chunk 4 did. Nothing else blocks it: the migration
work (`D225`(a)–(g), `D161`, the `rls_auto_enable` revoke, `D229`'s audit hardening) is unaffected,
and it now has a working publish-before-apply guard and `tier3-guard` in front of it.

**Also carried, from the panel, none of it a live risk:** the approved *body* is not pinned to the
approval (an issue is editable after labelling, and the daily cap deliberately holds an approved
issue overnight); the ERP lane is weaker than the `jahjah-dispatcher` lane already guarding the same
repo, tree and tmux session, which enforces author and tier; the hook classifies only a segment's
head word, so `for`/`xargs`/`find -exec`/`ssh`/`bash -lc` walk past it; `run-chunk.sh` defaults into
the *other* lane's state instead of failing closed; `.claude/settings.local.json` is gitignored yet is
half the chunk's permission surface; and **every successful ERP chunk is labelled `chunk:failed`**,
because the `/relay-report` skill that moves the label to `chunk:done` exists in the website repo and
not in this one — a failure signal that fires on success is one the owner learns to ignore.

```
=== RELAY ===
HEAD: d69ead99 | tree: clean (PR-D work on branch chunk4-pr-d, bfd64f9, unmerged)
CI: pass — post-merge main runs green for #87 (33707506283), PR-C #93 (33708891191) and #88 (33709609617); nine jobs each, tier3-guard correctly skipped on push
DONE: PR-C merged (tier3-guard sabotage-proven both ways, 14 action refs SHA-pinned, npm majors ignored, engines 22.x, AGENTS.md); riders #87 and #88 merged green, #89/#90 closed; ERP chunk lane built as a SECOND INSTANCE of the website's script with that lane's resolved config proven byte-identical, and smoke-proven from its timer (#95); GATE-1 rebuilt content-based and project-local with three of its own blockers closed; inbox lane retired; canon written (D228/D229/D230, CLAUDE.md, STRATEGIST, ROADMAP, STATE, registry, pitfalls) but NOT merged.
FILES: PR-C 5 files (merged). Branch chunk4-pr-d, unmerged. On the box: the shared lane scripts + erp-dispatch.env + units, /opt/jahjah/gate1/, health.sh; inbox artefacts removed.
FINDINGS/BLOCKERS: STOPPED on a panel blocker whose fix is itself a stop condition. A dispatched chunk runs as `obidex` (admin) and holds `Bash(gh issue *)`, so it can open and approve its own next chunk — the label gate does not bind the thing it gates. The collaborator invariant holds; the gate is open because the lane hands the executor the credential that operates it. The fix needs a separate label-blind token, i.e. a secret/auth change this chunk may not make. THREE OTHER PANEL BLOCKERS WERE MINE AND ARE FIXED: GATE-1 never fired on the documented wrapped apply (the exact hole D228 records the first version having, reproduced by me), the stdin lane skipped both halves, and GATE1_RELAY_CLONE was an override of the trust anchor contradicting three documents. The ERP lane is PAUSED via its kill switch; the website lane is untouched.
NEXT-NEEDED: one owner decision — give the chunk executor a separate GitHub token that cannot write labels, or accept in writing that the lane's real boundary is "the box is trusted" and that a chunk can extend its own run. Until then chunk 5 runs by paste; nothing else blocks the hardening migration.
=== END ===
```
