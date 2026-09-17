M1-engine is done: v3's machinery runs, with hooks on every session's edits and shell commands, a type-check on every PR, and builder and reader agents. `jahjah-web-run` dispatches `card:ready` issues every 2 minutes and proved the loop by closing a no-op card on its own in about 2 minutes. `think` runs on fable through Remote Control and answered the owner's phone. The chunk took one BLOCKED (type-check red on master, ruling A), two owner-reported start failures (both fixed and recorded as W169 and W170), and one incident: the executor started `think` for 20 seconds before its guard existed. Nobody should label an issue `card:ready` until M2 builds `/run-card`.

```
=== REPORT: M1-engine · done ===
HEAD: ef36b4c | tree: clean | branch: master
PRs: #117 ea144ca merged (T1 hooks + tsc) · #118 954291a merged (T2 settings v3 + think settings)
     #119 04d63db merged (T3 agents) · #121 485e250 merged (T4 dispatcher)
     #122 96d69e1 merged (T5 think.sh + Write-rule fix + owner re-copy) · #123 ef36b4c merged (T6 canon)
CI: master run 35234160046 success · PROD: deployment success | live probes: 1/1 (/ 200)
DONE: T0 labels (14 created; #114 chunk:proposed -> running)
      T1 post-edit/pre-bash hooks (91/91 cases); typescript ^5 + 47 libc; tsconfig excludes (ruling A); CI tsc
      T2 settings.v3.json (hooks, +9 allow, +4 deny) installed by the owner (W138 checks twice)
      T3 builder (sonnet), reader (haiku), reviewer header; each answered on its model
      T4 dispatch.sh + install.sh (simulation 43/43); owner installed; proof card #120 closed in 2 min
         (metrics comment, card:done, worktree removed, 1 run); then flipped to live workers
      T5 think.sh; think up (tmux `think`, fable, Remote Control); owner "Hello" answered 14:14:52Z
      T6 STATE/ROADMAP/DECISIONS (W168 LOCKED, W169 + W170 LESSONS; F52/F55 archived; F76-F79)
DEVIATIONS: tsconfig in T1 (ruling A); CI tsc after Build; post-edit uses node_modules/.bin/tsc;
      hook commands anchored at $CLAUDE_PROJECT_DIR; think.settings.json +11 denies (--settings is
      additive); `claude --remote-control` spelling (server mode refuses the flags); install.sh needed
      --dry-worker for the proof; a second STOP file in the state dir; preset --session-id per run
FINDINGS/BLOCKERS:
 1. BLOCKED once at T1: tsc red on master (5 errors in astro.config.mjs + product.ts) -> ruling A, F76.
 2. Owner-reported: Write(path) rules are not matched (headless runs only warn on stderr) -> W169.
 3. Owner-reported: tmux 3.4 pane commands need `=think:` -> think.sh killed its own session -> W170.
 4. Incident 10:42:55-10:43:17Z: the executor ran think.sh before its guard existed (a failed patch
    in the same command); killed in 20 s, noted on #114, guard since tested with a fake tmux.
 5. For M2 (F77-F79): think shares the executor's clone and inherits some project allows; broad
    allow rules (gh api methods, owner-run scripts, gh label); dispatcher gaps (no fetch, relabel
    stall, no log rotation). The ERP-side automations registry still lists -dispatch.
 6. Codex: over its usage limit on #118, #119, #121 and #122, silent on #117, 👍 (clean) on #123; the reviewer
    ran once per PR, 0 BLOCKs.
CANON: STATE (ledger, HEAD, flags 6/9/13/14, automations, NEXT STEP, HANDOVER), ROADMAP (F52, F55
       archived; F53; F76-F79; engine line), DECISIONS (W168-W170; W128), reference regenerated
NEXT-NEEDED: M2 brain (card #112); until then, no issue gets card:ready
=== END ===
```
