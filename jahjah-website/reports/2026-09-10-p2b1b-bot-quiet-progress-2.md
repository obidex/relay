T2 of chunk P2b-1b is merged: the five pending Dependabot updates are applied and `npm audit` fell from 20 to 16. The public site is byte-for-byte unchanged, including live. Two of the updated libraries are bundled inside the embedded Studio at /admin, so only its JavaScript changed. That missed one of the plan's checks ("dist byte-identical"), which could not be met with these packages. It is measured and reported below for ratification. T1's fix is confirmed working: Dependabot's first push after it got no Vercel build at all. All five bot PRs are now closed naming #76. T3, the canon close, is next.

=== REPORT: P2b-1b-bot-quiet · progress 2 (T2) ===
T1 OBSERVED AFTER MERGE
- Vercel: Dependabot rebased #70 at 21:44:55Z, 65 s after #75 merged. The new head, 63e6f82, sits on b3f2dc9 and carries the new vercel.json. It got 0 commit statuses and 0 GitHub deployments; its only check runs were two skipped `ci` runs. Before the change, every bot head got a Vercel status within seconds. So T1's acceptance holds: no Vercel deployment for a dependabot/** push.
- Dependabot: its npm job on the new master ran 21:43:54–21:47:01Z (success) and opened nothing. There was no grouped security PR, and #64–#66 were untouched. A 10-minute watch from 21:44:12 to 21:54:39Z saw no new bot PR.
- Security grouping: NOT YET OBSERVED. A second watch started when T2 merged; its result goes in the final report.

T2: PR #76, merged 0f79dc9 at 22:01:37Z (with --subject)
- package-lock.json only, for #63–#66. `npm update form-data smol-toml markdown-it json-2-csv` changed 7 version entries: those four, plus deeks 3.2.1, doc-path 4.1.4 and hasown 2.0.4, which they depend on.
- npm stripped all 29 `libc` fields again. All 29 are restored, by reinstating the pre-update entry wherever `libc` was the only difference (W123).
- `npm ci` from the committed lockfile exits 0 and resolves all four versions. package.json is byte-identical (sha256 unchanged).
- claude-review.yml, one line: claude-code-action d75b94d5 (v1.0.216) → 9c5ddab2e6d17b83ea679153b31f1d5f023cf636 (v1.0.217). Upstream's annotated tag v1.0.217 dereferences to that commit. As a control, v1.0.216 dereferences to today's pin. The diff's blob hashes (cdd67db..92b9bff) equal the bot's #70.
- Build 68 pages · verify 0 FAIL · 0 WARN · 67 pages · reference no drift.
- npm audit 20 → 16: high 12 → 10, moderate 6 → 4, low 2, critical 0. Resolved: form-data, json-2-csv, markdown-it, smol-toml. New: none.
- DIST NOT BYTE-IDENTICAL, measured:
  - All 67 non-/admin HTML files and every CSS/XML/JSON file are byte-identical.
  - admin/index.html differs in one line, the Studio entry script hash (Be7W1HaD → C06qqep-).
  - 14 Studio JS chunks changed, and no public page references any of them.
  - A rebuild is byte-identical, so the build is deterministic.
  - The cause is bundling: json-2-csv comes in via @sanity/vision and markdown-it via the Portable Text editor. The reviewer found json-2-csv's code in SanityVision.*.js and markdown-it's in lib.*.js.
  - The executor proceeded because the plan's intent, no site change, holds for every public page, and this is neither on the STOP list nor THE BAR (the reviewer agreed). FOR THE STRATEGIST TO RATIFY.
- Reviewer (executor's subagent): REVIEW: CLEAN, with 0 BLOCK, 0 FIX, 4 NOTE.
  - It re-derived the lockfile independently: the same 1,348 package keys, 7 entries differing, all 29 libc fields present.
  - It confirmed the dist deviation is Studio-only against production.
  - Its NOTEs were acted on: the PR body now says a grouped security PR is shut only if this lockfile already carries all of its updates; the preview URL was added; the commit message was scanned for closing keywords before committing.
- Codex: 👍 at 22:01:08Z, 3m35s after createdAt 21:57:33Z. No review, no inline comment, no issue comment.
- CI: green on PR #76, both on open and on the re-run after the body edit.
- Preview: /admin/structure loaded C06qqep-, and /, /ar/, /products/, /ar/products/, /about/ and /contact/ were byte-identical to production (the preview-only toolbar script stripped).
- Post-merge:
  - Vercel production for 0f79dc9 reported success at 22:02:01Z.
  - master ci run 34535311615 passed at 22:02:56Z.
  - Live /, /admin, /admin/, /admin/structure, /products/ and /ar/ all return 200.
  - Live /admin/structure serves C06qqep- and is byte-identical to dist/admin/index.html. Live / and /ar/ are byte-identical to dist.
- Bot PRs:
  - #64, #65, #66 and #70 are closed with "applied in #76 (P2b-1b)".
  - #63 had already been closed by T1's wording (progress 1, finding 1). It now carries the same comment plus a note on how it was closed.
  - No bot PR is open.
=== END ===
