T1 is done: Dependabot's grouped security update (#77) is applied in #80 and merged, and the bot's PR is closed naming it. The public site is byte-for-byte unchanged, and only the embedded Studio's JavaScript changed. `npm audit` fell from 16 to 11 findings. Codex answered with a 👍 in 1m51s. T2, the canon diet, is being written now.

=== REPORT: P2b-1c-canon-diet · progress (T1) ===
HEAD: 5cd1e40 | tree: clean | branch: chunk/p2b1c-t2-canon-diet (in progress, not pushed)
PRs: #80 5cd1e40 merged (T1) · Dependabot #77 closed with "applied in #80 (P2b-1c)"
CI: #80 `ci` green (1m7s); master post-merge run in progress at report time
PROD: live `/` 200; Vercel preview for #80 passed
DONE:
- T0: labels moved (chunk:running added, then chunk:proposed removed)
- T1: lockfile only. Versions moved: esbuild 0.27.7→0.28.2, vite 7.3.3→7.3.6 (astro's nested vite 8.2.2→8.3.0), @babel/core 7.29.0→7.29.7, nanoid →3.3.19/5.1.16, ws 8.20.0→8.21.3, plus tsx 4.21.0→4.23.13. 29 `libc` fields restored (W123). The lockfile is byte-identical to the bot's. package.json unchanged.
- Build 68 · verify 0 FAIL / 0 WARN / 67. `dist/`: all 67 public HTML byte-identical. Only admin/index.html changed (1 line) plus 16 Studio chunks, with 0 references from public pages (F61 acceptance).
- npm audit 16 → 11 (high 10→7, low 2→0). Resolved: @babel/core, esbuild, nanoid, vite, ws.
- Executor reviewer: one pass, CLEAN. Codex: 👍 at 23:00:24Z, 1m51s after `createdAt`.
DEVIATIONS: tsx is not in the bot's title. It moved because its `~0.27.0` range pinned esbuild. That move is inside `@sanity/cli`'s `^4.21.0`, and the bot's lockfile makes the same move.
FINDINGS/BLOCKERS: none
CANON: none in this PR (T2/T3)
NEXT-NEEDED: none
=== END ===
