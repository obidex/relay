T1 of M1-engine is merged: the edit and bash hooks exist, and CI now type-checks every PR, with the `tsconfig.json` scope from ruling A. The CI type-check ran in 5 s on both the PR and `master`, production redeployed cleanly, and the live site answers 200. The reviewer found three hook gaps (one false positive on commit-message heredocs, two missed spellings) and all three are fixed and covered by tests. Codex was silent for 10 minutes. Next is T2, the v3 settings file, which ends with your copy command.

```
=== REPORT: M1-engine · progress (T1) ===
HEAD: ea144ca | tree: clean | branch: master
PRs: #117 ea144ca merged
CI: master run 35201519827 success (Type-check 5 s) · PROD: deployment success | live probes: 1/1 (/ 200)
DONE: T1 · typescript ^5.9.3 dev; libc 29 restored + 18 from the registry · post-edit.sh
      (tsc / node --check / astro check if installed; the file's own checkout; never an npx install)
      · pre-bash.sh + pre-bash.mjs (91/91 cases) · tsconfig exclude per ruling A · CI Type-check after
      Build · reference regenerated · acceptance: clean .ts 0, broken .ts 2; rm -rf /etc/x 2,
      rm -rf dist 0; build 68; verify 0 FAIL 0 WARN
DEVIATIONS: post-edit calls node_modules/.bin/tsc rather than npx (without a terminal npx installs a
      missing package unasked); CI keeps npx tsc
FINDINGS/BLOCKERS:
 1. Reviewer: 3 FIX, 0 BLOCK, all fixed (a heredoc false positive; heads/master and glob refspecs;
    npx x@version spellings), plus cheap NOTEs. Codex: 👀 only, silent after the review-request
    comment (08:36Z to 08:46Z).
 2. Measured for T4/T5: `claude -p --worktree <name>` creates .claude/worktrees/<name> on branch
    worktree-<name> from master and leaves it locked after exit; the per-model usage field is
    `modelUsage`; `--resume` from the repo root re-binds the worktree, and runs in the main clone if
    the worktree is gone. The `claude remote-control` server mode refuses --settings/--model/--effort;
    `claude --remote-control "<name>"` accepts them; `fable` = claude-fable-5-1.
CANON: none yet (T6)
NEXT-NEEDED: none; T2 ends in WAITING FOR YOU (settings copy)
=== END ===
```
