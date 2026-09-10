T1 of chunk P2b-1b is merged. Vercel is now told not to build Dependabot branches, which were the source of every failed deployment the owner has seen. Dependabot is told to stop opening the Studio 5→6 upgrade PRs, because a named chunk (F34) already owns that upgrade. And npm security updates are set up to arrive as one grouped PR. The site itself did not change, and production is live and serving the same pages. One mistake of mine, reported below: the wording of my PR closed two bot PRs (#63 and #71) automatically when it merged. Nothing was lost, and T2 applies #63's update anyway. T2 (applying the pending bot updates) starts next.

=== REPORT: P2b-1b-bot-quiet · progress 1 (T0 + T1) ===
CHUNK: P2b-1b-bot-quiet · issue #74 · interactive, hand-started in auto mode
MODEL: Claude Code on Opus 5. The plan names Sonnet 5 (medium). The session model is fixed at start, and CLAUDE.md §4 says not to change it mid-chunk.

PREFLIGHT: all passed.
- pwd /opt/jahjah/web · CHUNK_ISSUE=74
- HEAD 5cdf390 == origin/master · tree clean · no stashes present · ls-files 84
- gh auth OK · ls-remote OK · npm ci OK
- build: 68 pages, exit 0
- verify: 0 FAIL · 0 WARN · 67 pages. The hidden-products check ran against Sanity, so the env names are present.
- No plan assumption contradicted STATE or the reference. The seven open bot PRs were the plan's.

T0: labels moved. chunk:running was added and confirmed (direct `gh issue view`), then chunk:proposed was removed and confirmed. chunk:approved was never applied.

T1: PR #75 · merged b3f2dc9 at 21:43:50Z, with --subject.
- Changes:
  - vercel.json: `"git": { "deploymentEnabled": { "dependabot/**": false } }`. The three /admin rewrites are untouched.
  - .github/dependabot.yml: npm `ignore` for sanity and @sanity/vision at version-update:semver-major, and an `npm-security` group (`applies-to: security-updates`, `patterns: ["*"]`). The comments are rewritten to match, including F60's 0.x caveat.
  - docs/reference/site.md: one row regenerated (vercel.json other keys: git).
- Acceptance checks:
  - JSON.parse exit 0 · rewrites 3 · PyYAML parse OK
  - minimatch over every remote branch: 7/7 dependabot/ branches match; master does not
  - build 68 · verify 0/0/67 · ci green in 57 s
  - Preview: /admin, /admin/ and /admin/structure were byte-identical to dist/admin/index.html. Only a direct /admin/index.html fetch carried Vercel's 163 B preview toolbar, as live flag 15 says.
- Codex: 👍 at 21:43:00Z, 2m21s after createdAt 21:40:39Z. No review, no inline comment, no issue comment. The 👀 was removed.
- Post-merge:
  - Vercel production for b3f2dc9: success at 21:44:11Z.
  - master ci run 34533766078: success.
  - Live /, /admin, /admin/, /admin/structure, /products/ and /ar/: all 200. Live /admin/structure is byte-identical to dist/admin/index.html (md5 d57ef7ac2187).

REVIEWER (the executor's subagent): three passes, and each verdict changed what shipped.
- Pass 1: 7 FIX + 4 NOTE, all comments that contradicted GitHub's docs. Every quote was re-checked on docs.github.com before rewriting.
- Pass 2: 3 FIX + 4 NOTE. The rewrite had claimed the two `ignore` entries hold back 6.x-only security fixes. dependabot-core's `ignored_versions` does `return versions if security_updates_only`, so an ignore rule's `update-types` never applies to a security job. The executor read the source and reversed the claim. This is read in the source, not observed here.
- Pass 3: 1 FIX, a PR-body count ("all three" branches when the remote has seven). Fixed by re-measuring.

BOT PRs after the merge:
- #72 (sanity 6.12.0) was closed by Dependabot itself at 21:43:53Z: "Looks like sanity is no longer being updated by Dependabot".
- #71 (@sanity/vision) and #63 (form-data) were closed by the EXECUTOR'S OWN PR TEXT (finding 1).
- #64, #65, #66 and #70 are still open.
- At 21:44Z Dependabot started npm and actions update jobs on the new master. Their result, and whether the first bot push after the merge creates a Vercel deployment, goes in the next report.

FINDINGS:
1. EXECUTOR ERROR: #75's body closed two bot PRs on merge. It said "the bot may close #63–#66 itself" and "The bot should close #71/#72 on its own". "close #N" is a GitHub closing keyword, so merging #75 closed #63 at 21:43:48Z and #71 at 21:43:49Z under the merging account. Dependabot answered #71 as it answers a human close ("I won't notify you again about this release"). #71 now carries the plan's comment and a note saying how it was closed. #63 gets its "applied in #<PR>" comment when T2 merges. The effect is small: #63's update is T2's anyway, and the major #71 proposed is now ignored by config. The lesson is one line: a PR body or commit message must never put close/fix/resolve directly before a PR or issue number. T3 records it.
2. The auto-mode classifier refused two commands. Both are reported, not routed around:
   - `mcp__claude_ai_Vercel__list_teams`, attempted for a bot-branch deployment baseline.
   - `ls .vercel/` combined with reading `.vercel/project.json`.
   The baseline was taken from GitHub instead.
3. Vercel records its GitHub deployments under the commit SHA, not the branch name. A first baseline filtered by ref prefix read 0, which was false. The true baseline is the Vercel commit status on the bot heads: failure on #71 and #72 (the Studio majors), success on #63–#66 and #70.

NEXT: T2 on chunk/p2b1b-t2-deps.
=== END ===
