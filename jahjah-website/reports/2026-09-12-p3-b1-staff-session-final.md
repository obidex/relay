A staff member can now sign in to the site with an email and password, enrol an authenticator app, type the six-digit code and reach `aal2` — the level the database already demands of every staff write — and the server knows who they are and what role they hold. There is no screen yet, by design; that is card #96. It is proven end to end against production, 40 checks out of 40, with both test accounts deleted afterwards and the database back exactly where it started. Two things written down as fact turned out not to be: previews carry no Supabase environment at all, and an MFA reset does not sign anybody out — both were measured, both are corrected in the canon, and the second leaves a residual the owner should see (F73). The 67 public pages are byte-identical to the build taken before the chunk began.

=== REPORT: P3-B1-staff-session · done ===
HEAD: 96aa8a3 | tree: clean | branch: master
PRs: #105 24bfda5 merged — `@supabase/ssr`, `src/lib/auth.ts`, middleware hook
     #106 fea51bf merged — the five `/api/staff/*` routes, `verify.sh` on-demand 1 → 6
     #107 e467302 merged — `staff-add`, `staff-mfa-reset`, `staff-remove`
     #108 80759a2 merged — `staff-smoke.mjs`, `db-smoke` stops comparing `audit`
     #109 96aa8a3 merged — canon close
CI: ci pass on every PR and on master (96aa8a3) · PROD: deployment success
    live probes 6/6: `/` `/ar/` `/products` `/brands` 200 · `/api/health` 200 · `/api/staff/me` 401
    `node scripts/staff-smoke.mjs https://jahjah-website.vercel.app` → 40 PASS, 0 FAIL, exit 0
    `node scripts/db-smoke.mjs` → exit 0; anon 42501 on all 7 tables, the view and the RPC
DONE: T0 labels · T1 cookie session library + middleware, one named dependency · T2 the five routes,
      login/logout/me/mfa.enroll/mfa.verify · T3 three owner-run admin scripts · T4 the end-to-end
      smoke · T5 canon
DEVIATIONS:
- `App.Locals.supabase` beside `App.Locals.staff`, so the middleware's client is reachable and no route repeats `getClaims`.
- `mfa/enroll` clears an unverified factor before enrolling, and its 409 carries `factorId`. Without the first, an abandoned enrolment locked the account out of enrolling; without the second, a staff member signing in from any later browser could never reach `aal2`.
- `me` answers 401, not 403-and-sign-out, for a session with no staff row — the plan's own spec for `me`, no state change on a safe method, and from P4 a customer's session survives a wrong turn.
- `staff-add` requires `--adopt` for an address that already exists (W080: a destructive action confirms); `staff-remove` and `staff-mfa-reset` refuse an account with no staff row.
- `scripts/db-smoke.mjs` stops comparing `audit`, under its own standing instruction to the plan that first enters data.
- Acceptance ran against the built function locally and then production, not the PR preview. F69 was rewritten though the plan said "F69 unchanged": it was factually false.
- DECISIONS net bytes 25556 against 25013, over by 543 — W167's and W161's own corrected content, rather than paying for it by dropping an ID.
FINDINGS:
- **W161 was wrong:** Preview has no `SUPABASE_URL` or anon key, so no preview can exercise a DB or auth route. Corrected, with F69.
- **W167's recovery design was wrong:** an MFA reset does NOT sign sessions out. Measured — the access token keeps `aal2` until it expires, the refresh token is not revoked, and the renewed token returns `aal1`. Writes stop within the token lifetime; the session does not. A lost device in someone else's hands needs `staff-remove` and a re-add. **F73, high.**
- Astro's `security.checkOrigin` is on by default: a POST to these routes needs `content-type: application/json` or a same-origin `Origin`, or Astro answers 403 first. The screens (card #96) must know this.
- `tier3-guard` does not cover `src/pages/api/**` (F74). TOTP enrolment passes no `issuer` (F75). A clean smoke run adds exactly 6 audit rows, measured.
- 10 Codex P2s and 3 reviewer BLOCKs, every one a real defect, all fixed. Two were sessions left live behind a refusal; one was a test account that could have been orphaned silently. Codex was silent on #107, #108 and #109 after the 5-minute wait and one `@codex review` each; the silence is recorded.
CANON: docs/STATE.md, docs/ROADMAP.md, docs/DECISIONS.md (W167 added; W161 corrected; W153/W154 compressed), docs/reference/site.md, README.md, scripts/verify.sh, scripts/db-smoke.mjs
NEXT-NEEDED: F73 — whether an MFA reset leaving a session alive is acceptable, or the owner shortens the access-token lifetime. Not a blocker for P3-B2.
=== END ===
