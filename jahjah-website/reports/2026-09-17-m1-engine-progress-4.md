T4 of M1-engine is merged, and the v3 dispatcher is running under systemd (`jahjah-web-run`, every 2 minutes). The old `jahjah-web-dispatch` timer is disabled. The no-op proof passed: card #120 was picked by a dry worker, got a metrics comment, and was closed and labelled `card:done` about 2 minutes after it was created, with its worktree removed and one run recorded. The dispatcher is still in dry-worker mode; the flip to live workers is the owner's next command, together with starting `think` (T5).

```
=== REPORT: M1-engine · progress (T4) ===
HEAD: 485e250 | tree: clean | branch: master
PRs: #117 ea144ca · #118 954291a · #119 04d63db · #121 485e250, all merged
CI: master run 35211764464 success · PROD: deployment success | live probes: 1/1 (/ 200)
DONE: T4 · dispatch.sh (one tick; STOP file, state-dir STOP or stop label; sleep; cap 8/day;
      MAX_PARALLEL 1; oldest unblocked card:ready; worktree worker, opus/xhigh for risk:3 else
      sonnet/medium; metrics comment; DONE/BLOCKED; usage-limit sleep and resume, 8-day cap;
      3 failures -> card:failed + STOP + alert) · install.sh (owner-run; refuses without a
      terminal or inside Claude) · simulation 43/43 · systemd-analyze verify clean
      · owner ran install.sh --dry-worker 10:32Z · proof card #120: picked 10:36:08Z, closed
      10:36:21Z, card:done, metrics comment (claude-sonnet-5 + haiku, 2 turns, 7 s), worktree
      removed, runs/ = 1 run
DEVIATIONS: the owner's line carried --dry-worker (the plan text omitted it, and the proof needs
      it); a second STOP file in the state dir; preset --session-id per run (measured to work)
FINDINGS/BLOCKERS:
 1. Reviewer: 2 FIX, 0 BLOCK, fixed (failure count reset on a dropped card; scripts/dispatch/STOP
    is untracked, so git clean could delete it, hence the state-dir copy). For M2: no git fetch
    before a worktree; a half-failed relabel can stall with no alert; no log rotation; the timer
    runs whatever branch the main clone has checked out.
 2. runs.log status column was cosmetic "DONEI"; fixed before merge (keyword only).
 3. Codex: usage-limit comment on #121.
CANON: none yet (T6)
NEXT-NEEDED: none; T5 ends in WAITING FOR YOU (flip + think + phone)
=== END ===
