# Chunk 3 — BLOCKED at T5: the review panel found blockers in both new security mechanisms

<!-- index: chunk 3 BLOCKED — PR-A merged and good; the GATE-1 hook and inbox lane failed the panel; the lane is DISABLED and PR-B is unmerged -->

**Stop condition met:** *"a panel blocker on T3/T4."*
**`main` HEAD:** `b4040fe` (PR #86, PR-A) — unchanged, green, and **not** affected by any blocker.
**PR-B:** committed and pushed as branch `chunk3-pr-b` (`ac8754b`), **no pull request opened, not merged.**
**The inbox lane is DISABLED on the work engine.** The GATE-1 hook is left active but must not be trusted as complete.

-----

## 1. What is done and safe

| | |
|---|---|
| **PR-A** [#86](https://github.com/obidex/jahjah-internal/pull/86) → `b4040fe` | **Merged, post-merge `main` 8/8 green.** `ci-ok`, Dependabot, the D229 grant audit. Nothing the panel found makes any of it unsafe. |
| **Ruleset 22122876** | **Active on `main`**, and proven live: Dependabot PR #87 (green `ci-ok`) reads `CLEAN`, #89 (red) reads **`BLOCKED`**. No test push to `main` was needed. |
| **Dependabot** | Live. Four PRs (#87–#90) open, **none merged**. |

## 2. The blockers — every one reproduced, not taken on report

### B1 · The GATE-1 kill switch is writable by the session it constrains

A chunk started by the inbox lane runs **as root with unrestricted `Bash`**. Its first call can be
`touch /opt/jahjah/GATE1_HOOK_OFF` — which is not a migration apply, so the hook allows it — and
every apply afterwards is allowed and merely logged `PAUSED`. `chmod -x` on the hook, or editing
`~/.claude/settings.json`, do the same.

**A PreToolUse hook cannot be gated by a file its own target can write.** This is a design fault,
not a patch: the pause needs to live outside the session's write scope, or not exist.

### B2 · The inbox provenance check does not prove the owner wrote the file

Two independent problems, and the second is the one that matters:

1. **`git log -1 -- <path>` follows history simplification.** For a **merge-commit** or **rebase**
   merge, the merge commit is TREESAME for a newly-added file and is pruned, so the command returns
   the *PR-branch* commit — whose author **and** committer an attacker sets locally. Only a **squash**
   merge sets committer to `GitHub`. **So this chunk's claim that "a merged PR is refused" is true for
   one of GitHub's three merge methods and false for the other two.** That claim is in the branch's
   `automations.md`, `infra-vps.md` and the published inbox report; it is corrected here.
2. **Author and committer are self-asserted git metadata.** Anyone who can push to the relay forges
   them in one command, using an email this repo publishes on purpose. The check proves "someone with
   push access produced a commit claiming to be the owner", never "the owner approved this".

The SHA-256 line is an **integrity** control, not an authenticity one — an attacker hashes their own
body. `SESSION:` and `MODEL:` are attacker-controlled lines.

**The real fix needs a verified signature** (`git verify-commit` against a key pinned in the script),
which requires the owner to provision a signing key. **That is an owner decision and is why this
stops here rather than being patched.**

### H3 · Command injection into the tmux window — root, outside `claude` and outside GATE-1

`inbox.sh` interpolates the model id and the chunk name into a single-quoted string handed to
`tmux new-window`, which `/bin/sh -c` executes **in the poll process, as root, before `claude` starts**.
A single quote breaks out. Reproduced verbatim:

```
the string inbox.sh would hand to tmux:
  CHUNK_NAME='CHUNK-9' CHUNK_MODEL='m';touch${IFS}/tmp/inbox_rce_probe;'' WORKTREE='…' … 'CHUNK-9'
  >>> CONFIRMED: arbitrary command executed. H3 is a REAL root RCE.
```

Note what this defeats: `--allowedTools` never applies, and the GATE-1 hook never sees it, because no
Claude Code tool call is involved. **This is why the lane is disabled rather than left running.**

### H1 / H2 · The hook does not cover the apply flow it claims to cover

Reported by two reviewers independently, and the more damaging half is H2:

- **`node scripts/db-query.mjs <migration file>` is not matched at all** — the head word is `node`,
  and that is one of the project's two documented database lanes (`CLAUDE.md` §1).
- **The house apply form does not name a migrations path on the `psql` line.**
  `docs/runbooks/backup.md` §3 wraps the file externally (`begin;` + body + `schema_migrations` insert
  + `commit;`), and the repo's own helper copies to `/tmp/replay.sql` first. The hook watches
  `psql -f supabase/migrations/x.sql`, a shape the documented procedure does not emit.
- Also missed: `bash -c '…'`, `~/…` tilde paths (bash expands them, `shlex` does not), and
  `cat <file> | psql`.

**So the canon sentence this chunk was to add — that the hook "refuses any apply whose hash is not on
the relay" — would have been an overstatement in `CLAUDE.md` and `STRATEGIST.md`. It has NOT been
merged.** The published hook report has been reissued with a correction banner.

## 3. What was fixed and verified before stopping

**Two review findings were real defects in this session's own work and are fixed in the branch:**

1. **A public page was republishing the hook's decision log.** `HEALTH-daily.md` goes to the public
   relay, and its own header promises "COUNTS and AGES, never identities" — while `gate1-hook.sh`
   promises its log "stays on the box; never published". My health.sh addition published a raw
   90-character tail of that log, breaking both promises in one diff. Now only a **whitelisted
   decision kind** is published (`ALLOW`/`DENY`/`PAUSED`/`PARSE`) plus a count. Verified live.
2. **The hook's tokenised path was more permissive than its own fallback.** A migrations path reached
   through a shell variable was allowed, needing no unusual syntax. Reproduced, fixed, re-verified:
   both forms now deny, with no regressions across the 18-case battery.

**A third finding stopped me shipping a bad fix.** I was mid-way through making the grant audit
verb-aware (the audit checks only `SELECT`, while 42 of 58 relations are written to through the
session client — a migration granting `SELECT` and forgetting `DELETE` would pass). The Postgres
reviewer caught that the obvious fix is **wrong**: this repo grants writes at **column level** on at
least eight tables, where `has_table_privilege` returns false. The correct predicate is
`has_any_column_privilege`. **The half-finished change has been reverted**; `scripts/grant-audit.sh`
on `main` is untouched and still passes.

## 4. State of the work engine, left safe

```
jahjah-inbox.timer     disabled / inactive
/opt/jahjah/INBOX_DISABLED   present, carrying the reason
unit ConditionResult   no        (a manual `systemctl start` is refused)
script guard           fires     ("skipped — INBOX_DISABLED present")
relay inbox            empty apart from the two .gitkeep files
```

Stopped at three independent layers, and nothing is queued. **No real chunk was ever started by the
lane**, in this session or at any point.

**The GATE-1 hook is left ACTIVE.** It still refuses the obvious accidental form and costs almost
nothing; disabling it would remove what protection exists. But per B1/H1/H2 it is **not** a complete
gate, and nothing in merged canon claims that it is.

## 5. What chunk 4 needs from the owner before any of this can proceed

Three decisions, none of which an unattended session should make:

1. **Signing.** Should the inbox lane require a **signed** commit (`git verify-commit` against a
   pinned GPG/SSH key)? This needs a key the owner provisions. Without it, the lane's authenticity
   check cannot be made sound, and the honest alternative is that the lane's real security boundary
   is simply "who can push to the relay".
2. **The kill switch.** GATE-1's pause must move outside the constrained session's write scope, or be
   removed. Both are architecture calls.
3. **Whether the gate belongs in the shell at all.** Two reviewers independently concluded that a
   command-text matcher cannot bound a session that owns the machine, and that the enforceable place
   is the database (an event trigger or an apply wrapper that refuses DDL whose migration hash is not
   on an allowlist). That is a different and larger design than this chunk assumed.

**Ready to land once those are settled**, all already written in `chunk3-pr-b`: the H3 input
validation (pass values via `tmux -e`, validate the model against `^[A-Za-z0-9._-]+$` and the name
against `^CHUNK-[0-9]+$`), the one-strike-per-pass cap, box-local rather than relay-hosted
concurrency markers, and the corrected canon wording.

**Also carried to chunk 4 from the panel, on the already-merged PR-A** (none of it a live risk, all of
it strengthening the D229 gate): make the audit **verb-aware using `has_any_column_privilege`**; flag
functions that are EXECUTE-able only via `PUBLIC`/`anon` rather than reporting them `OK` (four
already are, including the `SECURITY DEFINER` `rls_auto_enable` already on chunk 4's list); assert
`has_schema_privilege(… 'public','USAGE')`; strip `!hint` from embed targets; make
`GRANT_AUDIT_ADVISORY` downgrade only exit 1, never exit 2 (a broken audit must still fail the
build); and label the `--psql` live lane non-authoritative, since the live instance's default
privileges mean it can barely fail.

## 6. Two operational lessons, both paid for

**A broken user-scope PreToolUse hook takes its own kill switch down with it.** I put an apostrophe
in a comment inside the hook's embedded Python — the Python lives in a single-quoted bash string, so
the quote closed early and the file stopped parsing. A hook that exits non-zero blocks the tool call,
so **every `Bash` call on the box was refused, including `touch /opt/jahjah/GATE1_HOOK_OFF`.** The
only way out was a non-Bash editor. This also took out the **website executor's session** for several
minutes, which is how it surfaced; it is now written into the file's header.

**A user-scope hook is shared infrastructure.** It applies to every `claude` on the box, not just the
session that installed it. Validate with `bash -n` before installing, never edit in place.

## 7. Two chunk-start lanes now exist on this box

Worth the owner hearing before a third appears. The website executor built `jahjah-web-dispatch` the
same afternoon: it starts **website** chunks from GitHub issues labelled `chunk:approved`, in tmux
session `web`. This chunk built the relay-inbox lane for the **ERP**, in tmux session `jahjah`.
Different repos, different trigger surfaces, different sessions — **no mechanical collision** — but
two different designs for the same idea, and only one of them (theirs) is currently enabled. Both
sessions are reporting it.

```
=== RELAY ===
HEAD: b4040fe9112563d7829f13f56d41355414eac9e7 | tree: clean (PR-B work committed on branch chunk3-pr-b, ac8754b, unmerged)
CI: pass — main run 33648950432, 8/8 green including ci-ok. No CI run for PR-B; no PR was opened.
DONE: PR-A merged (ci-ok, Dependabot, grant audit — blocking). Ruleset 22122876 active and proven live (#87 CLEAN vs #89 BLOCKED). GATE-1 hook built, tested, two panel defects fixed, left ACTIVE but incomplete. Inbox lane built and fully acceptance-tested, then DISABLED after the panel. Canon edits, registry rows and pitfalls notes written and committed to the branch, NOT merged.
FILES: PR-A 5 files (merged). Branch chunk3-pr-b 14 files (unmerged). On the box: /opt/jahjah/bin/{gate1-hook.sh,inbox.sh,inbox-run.sh}, /opt/jahjah/inbox/, jahjah-inbox units (disabled), ~/.claude/settings.json hooks key.
FINDINGS/BLOCKERS: STOPPED on the "panel blocker on T3/T4" condition. B1 the GATE-1 kill switch is a plain file the constrained root session can write, so the hook cannot bound a chunk. B2 the inbox provenance check does not prove authorship — `git log -1 -- path` returns the attacker-controlled branch commit for merge-commit and rebase merges, and author/committer are self-asserted anyway; this chunk's "a merged PR is refused" claim is true only for squash merges. H3 confirmed ROOT RCE via a quote in the MODEL line or filename, executed outside claude and outside GATE-1 — the lane is disabled at three layers because of it. H1/H2 the hook misses `node scripts/db-query.mjs`, `bash -c`, tilde paths and a pipe into psql, and the externally-wrapped apply form backup.md §3 prescribes names no migrations path on the psql line — so the canon sentence describing the hook was an overstatement and was NOT merged. Fixed and verified before stopping: a public page was republishing the hook's log (it contradicted two promises in the same diff), and the hook allowed a migrations path reached through a shell variable. A fourth finding stopped a bad fix shipping: the grant audit checks only SELECT while 42 of 58 relations are written to, but the obvious fix is wrong because this repo grants writes at COLUMN level — the half-finished change was reverted.
NEXT-NEEDED: three owner decisions before chunk 4 can touch this again — (1) will the owner provision a signing key so the inbox lane can require a verified signature, or is "who can push to the relay" accepted as the real boundary; (2) where should GATE-1's pause live, given it cannot be a file the constrained session can write; (3) should the migration gate move to the database (event trigger / apply wrapper) rather than a shell-command matcher, as two reviewers independently concluded. Chunk 4's migration work (D225 a–g, D161, rls_auto_enable) is unaffected and can proceed by the normal pasted-prompt route.
=== END ===
```

-----

## ADDENDUM (2026-09-02, after publication) — two items from the website executor that bear on chunk 4

Recorded here rather than left in a session log, because chunk 4 will read this report and both change
what it can assume.

**1. The H3 injection class was not confined to this lane, and the fix is now proven on the other one.**
The website executor checked `jahjah-web-dispatch` against the reproduction above and found the same
*shape* — six values interpolated into the string handed to `tmux new-window`. All six were constants
or a GitHub-issued integer, so nothing was exploitable, but that was a property of today's inputs
rather than of the script. It now validates the issue number as digits at the point it leaves `jq`,
asserts the model is one of two literals, and launches with `tmux new-window -e VAR=value` flags and
the script as a bare argument, so **no shell string is built at all**. That is the same fix queued in
`chunk3-pr-b` for the ERP lane, and it is now proven to work on a live lane. **Its lane has since run
end to end from its timer** (issue labelled → picked up in 48s → dispatched into tmux `web` → exit
code reported → relabelled), so ours is the only one of the two that is disabled.

**2. An authenticity lesson from a second angle, and it generalises B2.**
That session had written that its `chunk:approved` label gate "requires write access"; a reviewer
corrected it — GitHub's **Triage** role can label an existing issue with **no push access at all**.
So its real gate is "at least Triage on a private repo", now recorded as an external invariant the
job depends on and cannot itself enforce.

That is the same defect as `B2` reached from the other direction, and the pair is worth stating as one
rule for anything that starts privileged work: **a control that rests on metadata, a label, or a
self-asserted identity is a convention, not a boundary — and it must be written down as an assumption
about somebody else's system rather than as a property of ours.** `B2` claimed authorship from git
metadata; theirs claimed authorship from a label. Neither survived a reviewer. `CLAUDE.md` §4 already
says the equivalent thing about client input; this is that rule applied to a trigger surface.

**3. A shared relay-publishing wrapper exists — with one caveat if the inbox lane returns.**
`/opt/jahjah/session/publish-report.sh <project>/reports/<name>.md <local-file>` takes the shared
relay `flock` properly via `jj_relay_sync` / `jj_relay_push`, redacts, rebuilds that folder's
`INDEX.md`, and prints the raw URL, with path guards and a 512 KB ceiling. It lands in
`infra/vps/session/` on `main` shortly. **Caveat before adopting it here: it hardcodes
`JJ_JOB=session`, so two lanes publishing concurrently would share `/opt/jahjah/session.log` and one
state directory** — which is exactly the kind of shared-state collision the relay lock exists to
prevent. Worth reading before the inbox lane reuses it instead of its own locking.
