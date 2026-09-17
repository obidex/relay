T2 of M1-engine is merged: settings v3 is in force (the owner's copy is committed), and the hooks are live in the running session. The `think` settings file is in, with one measured tightening. `--settings` adds to the project settings, so `think` would have inherited every project allow rule (merges, pushes, `git checkout`), and 11 deny rules now keep it read-and-canon only. CI and production are green, and Codex reported that it has used up its review quota. Next is T3, the three agent files.

```
=== REPORT: M1-engine · progress (T2) ===
HEAD: 954291a | tree: clean | branch: master
PRs: #117 ea144ca merged · #118 954291a merged
CI: master run 35204140199 success · PROD: deployment success | live probes: 1/1 (/ 200)
DONE: T2 · settings.v3.json (hooks, +9 allow, +5 deny, each once) · owner cp verified per W138
      (porcelain = that one file, cmp identical, parses) · hooks live (pre-bash refused a probe
      `supabase db reset --help`) · think.settings.json (18 allow, 8 + 11 deny) · build 68 · verify 0/0
DEVIATIONS: (1) hook commands use "$CLAUDE_PROJECT_DIR"/scripts/dispatch/hooks/…, not a bare relative
      path (a moved cwd would silently disable them); (2) think.settings.json +11 deny rules (below)
FINDINGS/BLOCKERS:
 1. Measured with claude -p --settings think.settings.json --permission-mode dontAsk: project allow
    rules apply too (git worktree, git branch ran). Added denies: git checkout, git mv, chunk
    pushes, gh pr merge, git worktree, gh api, gh label, gh workflow run, bash, the verify.sh
    spelling. Re-measured: git worktree denied, git status runs.
 2. For M2: think runs in the SAME clone as the executor and the dispatcher, whose unit reads
    dispatch.sh from that checkout, so a `git switch -c canon/*` by think changes the branch under
    both. A separate clone or worktree for think is worth a card.
 3. Reviewer NOTEs to register at T6: `gh api repos/…/*` allows any HTTP method (a ruleset DELETE
    matches); `Bash(bash scripts/dispatch/*)` would auto-allow install.sh (T4 adds a terminal
    guard); `npx tsc` in a checkout with no node_modules installs an unrelated package; `gh label:*`
    allows label deletion.
 4. Codex: "usage limits" comment on #118 at 09:11Z; no review. The reviewer was CLEAN
    (0 BLOCK, 0 FIX, 11 NOTE).
CANON: none yet (T6)
NEXT-NEEDED: none
=== END ===
```
