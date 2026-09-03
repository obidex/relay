# Chunk 5 — preflight: the lane works, and it cannot do half of what this chunk asked

<!-- index: chunk 5 preflight — the ERP lane dispatched its first real chunk; a dispatched session turns out to be sandboxed to its own working tree -->

**Chunk issue:** [#96](https://github.com/obidex/jahjah-internal/issues/96) · dispatched by
`jahjah-erp-dispatch` at 2026-09-03T20:23:05Z, approved 20:18:55Z — **4 minutes 10 seconds from the
owner's label to a running chunk.** This is the lane's first real chunk; #95 was a smoke test.

-----

## 1. Preflight — every gate green

| Check | Result |
|---|---|
| `pwd` | `/root/jahjah-internal` |
| Working tree | clean |
| `git fetch` → `origin/main` | `d69ead995207ccda34b5486c2599df3c4a261f60` |
| Branch on start | `chunk4-pr-d` at `bfd64f9`, and **`main` is already its ancestor** — no rebase needed |
| tmux | session `jahjah`, running in window **`chunk-96`** (the lane started me; the pause file is absent, left alone per decision B) |
| **Label-gate invariant** | `collaborators` → **`obidex`** only · `invitations` → **0**. Holds. |
| Repo ↔ box drift | **zero** — `git hash-object` matches on all six live files (`web-dispatch.sh`, `run-chunk.sh`, `erp-dispatch.env`, `health.sh`, `publish-report.sh`, `gate1-hook.sh`) |

## 2. THE FINDING: a dispatched chunk is sandboxed to its own working tree

Measured, not inferred, and it governs everything below.

```
touch /opt/jahjah/erp-dispatch.log
  -> blocked: "Claude Code may only create or modify files in the allowed
     working directories for this session: '/root/jahjah-internal'"

mkdir -p .claude/skills/relay-report      ·   touch .claude/settings.json
  -> permission request, unanswerable in a headless run.
     The .claude/** guard is PATH-based and applies to Bash too, not only
     to the Write and Edit tools.

bash -n <script>
  -> `bash` is not in .claude/settings.json's allow list.
```

**So a dispatched chunk cannot install anything to `/opt/jahjah`, cannot create or edit a skill or a
settings file, and cannot syntax-check a shell script.** It *can* edit `infra/vps/` — the source of
record — which means **a chunk can produce repo-ahead drift it is structurally unable to deploy or
verify.** That is the shape of the problem, and it is new information about the lane, not about this
chunk.

**I did not route around any of the three.** A `cp` from a scratch path, or a `node -e` that spawns a
shell, would walk past guards that exist precisely to stop a chunk widening its own permissions —
which is `D231`'s own subject. The `.claude/**` guard is itself an accident guard rather than a
boundary (path-based, over the commands it can parse), and the correct response to an accident guard
firing is a stop and a report.

### What that changed in the plan

| Step | Disposition |
|---|---|
| **T0.3** port `/relay-report` | **BLOCKED.** The skill is written — full text in §4 below — but cannot be placed at `.claude/skills/relay-report/SKILL.md`. Reporting is done by hand instead, following the skill's own procedure. |
| **T0.3** allow-list rules | **NOT DONE, and deliberately not worked around.** The four `gh issue` forms the skill uses are already permitted here by the project-local `.claude/settings.local.json` (gitignored) — proven by this chunk executing every one of them. Moving them onto the committed surface is a widening of the chunk's own permission surface, i.e. exactly `D231`'s shape, so it is left to the owner. |
| **T1** the lane fixes | **WRITTEN AND PANELLED, NOT INSTALLED, NOT SABOTAGE-CHECKED.** They live on branch `chunk5-t1-lane-fixes` and are **deliberately kept out of PR-E** — see §3. |
| **T2** land chunk 4 | **UNAFFECTED.** All repo work; proceeding in full. |

## 3. Why the lane fixes are not in PR-E

`ci-ok` does not lint shell — there is no `shellcheck` on this box and no shell job in the pipeline —
so a green CI would say nothing about these two scripts. Merging unparsed, uninstalled, unproven
shell for a **live root-privileged dispatch lane** into `main` would mean the next person to run
`infra/vps/README.md`'s copy commands installs it. The chunk's own acceptance criteria for T1 —
`bash -n` before and after, an atomic install, a sabotage check, a proof that the website lane's
resolved config is unchanged — are all unreachable from here.

So: the design ships as a reviewed branch, the box keeps running the code it has, and the registry
and `docs/STATE.md` say in as many words that the check is **not live**. The exact install and
sabotage-check commands are handed to the next session.

**PR-E therefore contains zero drift:** every `infra/vps/` file in it is byte-identical to its live
copy under `/opt/jahjah`, verified by hash.

## 4. The ported `/relay-report`, for an interactive session to place

Save as `.claude/skills/relay-report/SKILL.md`. It is the website repo's skill with four
substitutions: the repository, the relay folder, the `CHUNK ISSUE` line's repository, and the
executor paths (`/opt/jahjah/web` → `/root/jahjah-internal`, `/opt/jahjah/chunks` →
`/opt/jahjah/chunks-erp`, `jahjah-web-dispatch` → `jahjah-erp-dispatch`). Two further changes are
deliberate and are flagged rather than hidden:

- the report body is this project's **RELAY BLOCK** (`=== RELAY ===` … `=== END ===`,
  `docs/STRATEGIST.md` §1), not the website's `=== REPORT: … ===` shape;
- the note about `gh issue view` says what is true **here** — it is granted by the project-local
  settings on this box, and is *not* on the committed allow list, so it must not be relied on
  elsewhere.

The full text is attached to this chunk's issue as a comment, because a 14 KB code block in a public
relay file is the wrong shape for a report. `gh issue view 96 --repo obidex/jahjah-internal` has it.

## 5. Also worth the owner's eye, unrelated to this chunk

**`docs/STATE.md` is served publicly at `/internal/docs`, and it contains the work engine's SSH
line — user and IP.** It is already on `main`, so it is already public; it is not new and not this
chunk's doing. It is worth noting because the relay's own publisher **redacts that same IP from every
file it publishes**, so the canon mirror publishes what the relay is careful to strip. Fixing it
means either dropping the value from `STATE.md` or redacting in the docs lane — a change to files
outside this chunk's list, so it is reported rather than done.

```
=== RELAY ===
HEAD: d69ead99 (main) | tree: clean | running as chunk-96 in tmux jahjah
CI: not yet run for PR-E — this is the preflight
DONE: preflight green (tree clean, HEAD recorded, collaborators = obidex only + 0 invitations, zero repo/box drift on all six live files); the ERP lane dispatched its first REAL chunk 4m10s after the owner's label; `/relay-report` ported in full but NOT installable.
FILES: none committed yet at preflight. Branch chunk5-t1-lane-fixes pushed (9dd4fb6) with the lane fixes, marked DO NOT MERGE.
FINDINGS/BLOCKERS: A DISPATCHED CHUNK IS SANDBOXED TO ITS OWN WORKING TREE — it cannot write to /opt/jahjah, cannot create or edit anything under .claude/, and has no `bash` (so no `bash -n`). Measured three ways. T0.3's skill install and T1's install + sabotage checks are therefore undeliverable from a dispatched session; T1 is written and panelled but kept OUT of PR-E rather than merged unverified, because ci-ok does not lint shell. None of the three guards was routed around. Separately: docs/STATE.md is publicly mirrored and carries the box's ssh user and IP, already on main — reported, not fixed.
NEXT-NEEDED: none to finish this chunk. For the next one: an interactive (or --add-dir) session to install and sabotage-prove the lane fixes and to place the skill.
=== END ===
```
