# Chunk 3 — the INBOX LANE: a chunk starts from a file the owner pushes

<!-- index: chunk 3 — inbox lane built and fully acceptance-tested; timer enabled, inbox empty, NO real chunk started -->

**Unit:** `jahjah-inbox.service` + `jahjah-inbox.timer` (every 5 min) · **Scripts:**
`/opt/jahjah/bin/inbox.sh`, `/opt/jahjah/bin/inbox-run.sh` · **Kill switch:** `/opt/jahjah/INBOX_OFF`
**Log:** `/opt/jahjah/logs/inbox.log` · **Decision:** `D228` · **Pilot:** chunk 4

**NO REAL CHUNK WAS STARTED.** Every fixture below was a test file, and every acceptance run was
either a rejection or a dry run.

-----

## What it is

The owner pushes `jahjah-internal/inbox/CHUNK-<n>.md` to the relay. Within five minutes the lane
validates it and starts a chunk from its body in the tmux session `jahjah`. It replaces the paste.

**The body becomes the prompt of a fully-privileged executor and there is no approval step after
it — the owner's push IS the approval.** That is the design, and it is why every check fails closed.

## The four checks, each proven by a fixture the TIMER picked up

Never by running the script by hand (`automations.md` §6.5).

| Fixture | What was wrong | Verdict |
|---|---|---|
| `CHUNK-0` | valid hash, valid model, but `SESSION: test` | **REFUSED** — *no `SESSION: jahjah` line (a chunk is never started in the `web` session)* |
| `CHUNK-1` | nothing — fully valid | **VALIDATED, NOT STARTED** (dry run) |
| `CHUNK-2` | valid content, committed by `Someone Else <someone@example.invalid>` | **REFUSED** — *author is `Someone Else <…>`, not the owner `obidex <144545793+obidex@users.noreply.github.com>`* |
| `CHUNK-3` | owner's commit, but a line appended after hashing | **REFUSED** — *body sha256 is `802e2f22…` but line 1 declares `a6db0a95…` — the file was altered after it was hashed* |
| `CHUNK-4` | valid in every way except no `MODEL:` line | **REFUSED** — *no `MODEL: <model id>` line* |

`CHUNK-2` is the important one: it is the security boundary, and it is proven rather than assumed.

### The dry run started nothing — checked three ways

```
2026-09-02T16:02:36Z INFO  DRY RUN — CHUNK-1.md passes every check; would start it in tmux
                           session jahjah with model claude-opus-5

state files on origin/main:   only .gitkeep and CHUNK-0.rejected  (NO CHUNK-1.started)
tmux windows in session jahjah:  claude OS workflow, bash          (NO chunk-1 window)
heartbeat:  | Dry run | YES — validates and reports, never starts |
            | Chunk in flight | none (dry run: **would start CHUNK-1**, model `claude-opus-5`) |
```

### The three-strike law and the documented recovery, both exercised

`CHUNK-4` was deliberately the third consecutive refusal:

```
2026-09-02T16:26:36Z ERROR third consecutive failure — INBOX_DISABLED written
2026-09-02T16:26:36Z ERROR failure 3/3: CHUNK-4.md rejected: no `MODEL: <model id>` line
2026-09-02T16:26:36Z ERROR FAILURE CAP: 3 consecutive failures — disabling jahjah-inbox.timer
Removed "/etc/systemd/system/timers.target.wants/jahjah-inbox.timer".
```

`/opt/jahjah/INBOX_DISABLED` was written, the timer went `disabled`/`inactive`, and
`ALERT-inbox-disabled.md` was published. This was worth exercising rather than trusting: the
three-strike law itself is shared library code that eight fleet jobs already use, but the
`INBOX_DISABLED` marker and the `strike()` wrapper are **new code written for this lane**, and the
service additionally carries `ConditionPathExists=!/opt/jahjah/INBOX_DISABLED` so a re-enabled timer
still will not run while the marker is there.

The recovery in the README was then followed exactly, and the lane came back.

## Two defects found in my own draft, before it ever ran

1. **A third strike would have destroyed its own evidence.** `jj_note_failure` can trip
   `jj_trip_failure_cap`, which begins with `jj_relay_sync` → `git reset --hard origin/main`. That
   would have wiped the `.rejected` markers written during the same pass but not yet pushed. Strikes
   are now **deferred until after the push**, so the evidence is on `origin/main` before the cap can
   fire. (The `CHUNK-4` run above confirms it: all three `.rejected` markers survived.)
2. **The kill-switch branch took the shared relay lock for nothing** and left a dead placeholder
   function, which would have re-taken `flock` on the same fd later in the same run. Removed.

## Findings the strategist needs before the chunk-4 pilot

**1. A GitHub web-UI commit WILL be refused — this is the likeliest way the pilot fails.**
The check requires **author AND committer** to be the owner. A file added or edited through
github.com commits as author `obidex` but committer **`GitHub <noreply@github.com>`**, and is
refused. So is anything merged via a PR. **The owner must push with git**, e.g.

```
git clone https://github.com/obidex/relay && cd relay
cp <the chunk file> jahjah-internal/inbox/CHUNK-4.md
git add -A && git commit -m "chunk 4" && git push
```

This was implemented exactly as the plan specified. It is the strongest form of the control, and it
is also the most likely operational surprise, so it is stated here rather than discovered on the
day. **If the strategist would rather accept a web-UI push, that is a one-line change** — but it
weakens the control to "someone with write access", since the web UI's committer is a constant.

**2. Root cannot use the bypass permission flags, so the runner uses an explicit allow-list.**
Both `--dangerously-skip-permissions` and `--permission-mode bypassPermissions` are refused outright:

```
--dangerously-skip-permissions cannot be used with root/sudo privileges for security reasons
```

The box runs as root, so `inbox-run.sh` grants the tools a chunk needs via `--allowedTools` instead.
This is a mechanism, not a preference, and it is why the lane's real controls are the **GATE-1 hook**
(verified to bind `-p` sessions), the **`main` ruleset**, and the owner's push.

**3. The lane examines one file per pass and stops at the first acceptable one.** That is
one-chunk-at-a-time working correctly, but it means rejections queued behind an acceptable file wait
their turn — visible above, where `CHUNK-2`/`CHUNK-3` were not looked at until `CHUNK-1` was removed.

**4. Stated plainly: anyone who can push to the relay AS the owner's identity can start a chunk** —
including this box, whose git config uses exactly that identity because Vercel requires it
(`docs/pitfalls/infra-vps.md`). This is not a new hole: a box that could forge that push already runs
the executor. The checks stop a *mistaken* or *injected* file, not a compromised work engine.

## Left in this state

```
timer enabled : enabled          NEXT: a real time (armed, not merely enabled)
last result   : success          last poll: "done in 3s — inbox empty"
strike counter: 0
INBOX_DISABLED: absent           INBOX_OFF: absent           dry run: off
relay inbox   : jahjah-internal/inbox/.gitkeep, jahjah-internal/inbox/state/.gitkeep  (nothing else)
inbox ALERTs  : none
```

Every fixture, every state marker, both alerts and the temporary dry-run drop-in are gone.
`HEARTBEAT-inbox.md` remains, as the standing proof-of-life file.

```
=== RELAY ===
HEAD: b4040fe9112563d7829f13f56d41355414eac9e7 | tree: clean
CI: pass — main run 33648950432, 8/8 green including ci-ok
DONE: inbox lane built, installed and acceptance-tested end to end — jahjah-inbox.service/.timer (5 min), /opt/jahjah/bin/inbox.sh + inbox-run.sh, relay inbox/ + inbox/state/ folders, README. All four checks proven by timer-driven fixtures; dry run proven to start nothing; three-strike self-disable and its documented recovery both exercised. Lane left ENABLED, armed, counter 0, inbox empty. NO real chunk started.
FILES: /opt/jahjah/bin/inbox.sh, /opt/jahjah/bin/inbox-run.sh, /opt/jahjah/inbox/README.md, /etc/systemd/system/jahjah-inbox.{service,timer}, relay jahjah-internal/inbox/{,state/}.gitkeep
FINDINGS/BLOCKERS: (1) A GITHUB WEB-UI COMMIT WILL BE REFUSED — its committer is `GitHub <noreply@github.com>`, and the check requires author AND committer to be the owner. The owner must push with git. Implemented as specified; the likeliest way the chunk-4 pilot fails, and a one-line change if the strategist prefers otherwise. (2) Root cannot use --dangerously-skip-permissions or --permission-mode bypassPermissions, so inbox-run.sh uses an explicit --allowedTools list; the real controls are the GATE-1 hook and the ruleset. (3) Two defects found and fixed in my own draft: a third strike would have reset --hard away its own .rejected evidence, and the kill-switch branch took the relay lock for nothing. (4) Anyone able to push to the relay as the owner's identity can start a chunk, this box included — not a new hole, but stated.
NEXT-NEEDED: none — proceeding to T5 (PR-B: repo copies, registry, pitfalls, canon)
=== END ===
```
