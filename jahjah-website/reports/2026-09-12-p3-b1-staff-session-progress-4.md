P3-B1 T4 is merged and the whole chunk now has its proof: run against production, the staff session passes 40 of 40 checks — wrong password refused, a non-staff account refused with no cookie left behind, sign in, enrol an authenticator, verify a code, reach `aal2`, come back the next day and reach it again, sign out, and both throwaway accounts deleted with the database back where it started. The reviewer BLOCKED this task twice, both times on the one rule that matters here: a test account must never be left behind. The second BLOCK was the sharper one — a failed lookup was being read as "already deleted", on the path taken every single run — and it is closed. Only the canon close, T5, is left.

=== REPORT: P3-B1-staff-session · progress ===
HEAD: 80759a2 | tree: clean | branch: master
PRs: #105 24bfda5 · #106 fea51bf · #107 e467302 · #108 80759a2 — all merged
CI: ci pass on every PR · PROD: `https://jahjah-website.vercel.app` | staff-smoke after the merge: 40 PASS, 0 FAIL, exit 0
DONE: T0 labels · T1 session library · T2 the five routes · T3 the three admin scripts · T4 the end-to-end smoke
DEVIATIONS:
- The smoke ran against the built function locally and then against production, not the T2 PR preview the plan named. The preview has no Supabase environment at all (see the T2 report); Vercel env is owner-only, so this is reported rather than fixed.
- `scripts/db-smoke.mjs` stops comparing `audit`. Its own comment asked the plan that first enters data to update that line, and this is that plan: the audit log is append-only (W080), so its count only grows. The other six tables are still compared exactly.
FINDINGS:
- A clean smoke run adds exactly **6** audit rows, measured before and after (89 → 95), not the 4 first written down.
- Reviewer BLOCK 1: cleanup ran only if an id lookup had succeeded, so one API hiccup would have orphaned the account just created. Cleanup now works from addresses registered before the call that could create them, falls back to a direct admin delete when `staff-remove` refuses, re-checks, and prints any survivor by address; signals run the same cleanup.
- Reviewer BLOCK 2: `findByEmail` returned null for "absent" and for "the lookup failed" alike, and the fallback read that as "already deleted" — on the path taken every run, since `staff-remove` refuses the plain user by design. It now answers `{ user }` or `{ error }`, and an address that could not be checked is reported as a survivor.
- Codex was silent on #108 through two `@codex review` comments and both 5-minute waits; the silence is recorded and the merge went on green CI and the reviewer passes.
CANON: docs/reference/site.md, scripts/db-smoke.mjs. STATE/ROADMAP/DECISIONS close in T5.
NEXT-NEEDED: none
=== END ===
