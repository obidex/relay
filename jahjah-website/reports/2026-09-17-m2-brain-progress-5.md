M2-brain has merged its fifth PR, the engine fixes. `think` will run in its own worktree once the owner restarts it. The dispatcher now fetches before every card, gives each card a fresh `card-<n>` worktree, and rotates its logs. The live settings are narrower: the owner copied them, and the session committed his copy. The live dispatcher has already ticked cleanly on the new script. Only the canon close (T7) remains, then the owner's `think.sh --restart`.

=== REPORT: M2-brain · progress ===
HEAD: 5fa6858 | tree: clean | branch: master
PRs: #125 556b275 (T2) · #155 1a1b037 (T3) · #156 b340d18 (T4) · #157 fcc0af9 (T5) · #158 5fa6858 (T6), all merged
CI: #158 ci green; master run on 5fa6858 green · PROD: live / 200 | live probes: 1/1 · jahjah-web-run: idle ticks at 16:08/16:10 on the new script
DONE: T6 (a) think worktree + --restart · (b) fetch + `-b card-<n>` worktrees + 14-day rotation · (c) settings v3 narrowed, owner's copy committed (W138) · (d) think rules; #152 and #153 closed; #154 partly done (comment)
DEVIATIONS: think may also use WebSearch/WebFetch, for the strategist skill's live benchmarks. Codex usage-limit notice on #158. One compound command was refused because it contained a bare `git push` (deny rule); it was re-run as separate calls with `git push origin chunk/…`
FINDINGS/BLOCKERS:
- Measured with headless runs:
  - think's rules 22/22 + 4/4;
  - the v3 `gh api` and `--output` denies 10/10 + 8/8;
  - a worktree of this clone is trusted, inside or outside it;
  - resuming a session inside a card worktree works.
- Residual: think can create a local non-`canon/*` branch (through the project's `git checkout` rule); it cannot push one.
- Workers push `card-<n>` with no allow rule (the plan named none), so the auto classifier decides. M3 will show it.
CANON: none in this PR (settings owner copy) · NEXT-NEEDED: none until T7's final
=== END ===
