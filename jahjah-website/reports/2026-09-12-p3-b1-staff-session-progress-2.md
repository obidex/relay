P3-B1 T2 is merged and live: a staff member can now sign in, enrol a TOTP authenticator and reach `aal2`, proven end to end against the real project — 36 of 36 checks pass, and both throwaway accounts were deleted afterwards. Codex found four P2 defects across two rounds, all real and all fixed; the sharpest was that a returning staff member could never reach `aal2` at all, because the factor id needed to verify a code was only ever handed out once, on the day of enrolment. One canon fact turned out to be wrong: previews have no Supabase environment whatsoever, not merely no service key, so the acceptance was run against the built function locally and then against production instead of the preview. The 67 public pages are still byte-identical to the build before this chunk began. T3's admin scripts are next.

=== REPORT: P3-B1-staff-session · progress ===
HEAD: fea51bf | tree: clean | branch: master
PRs: #105 24bfda5 merged — session library + middleware · #106 fea51bf merged — the five routes
CI: ci pass on master (fea51bf) · PROD: deployment success | live probes: 5/5 on production — `POST /api/staff/login` with a wrong password 401, body exactly `{"ok":false}`, `cache-control: no-store`, no `set-cookie`; `/api/staff/me` 401; `GET /api/staff/login` 405; `/api/health` 200; `/` and `/ar/` 200
DONE: T0 labels · T1 session library · T2 the five routes, `verify.sh` on-demand 1 to 6, reference 24 routes / 6 on-demand
DEVIATIONS:
- `mfa/enroll` clears an UNVERIFIED TOTP factor before enrolling. The plan says "else `mfa.enroll`", but an abandoned enrolment keeps the friendly name, GoTrue refuses a second factor under it, and every retry became a 500 escapable only by the owner's reset script.
- The 409 body carries `factorId` as well as `code`. Without it a staff member signing in from any later browser could not reach `aal2`, because verification needs an id that was only ever returned by the first enrolment.
- `me` answers 401, not 403-and-sign-out, for a session with no staff row: the plan's own spec for `me`, no state change on a safe method, and from P4 a customer's own session survives a wrong turn into a staff URL.
- Acceptance ran against the built function and production, not the PR preview (see FINDINGS).
FINDINGS:
- **W161 is wrong, measured today.** It records `SUPABASE_URL`/`SUPABASE_ANON_KEY` as present in Vercel Production AND Preview. Preview has neither: the routes answer 500 there and the runtime log says `SUPABASE_URL is not set in the server environment`. F69 reads the preview 503 as "no preview DB"; it is no preview Supabase environment at all. Setting Vercel env is owner-only, so this is reported, not done. W161 and F69 are corrected in T5.
- TOTP enrolment is enabled on the project. The plan's conditional BLOCK does not apply.
- Astro's `security.checkOrigin` is on by default, so a POST to these routes must carry `content-type: application/json` or a same-origin `Origin`, or Astro answers 403 before the route runs. A useful extra CSRF layer, and a constraint the screens (card #96) must know.
- `scripts/db-smoke.mjs` expects `audit=0`; a smoke run leaves audit rows by design. Its own comment says the plan that first enters data updates that line, and T4 does. It is not in CI.
- Four Codex P2s, all fixed: `signOut` defaulted to the `global` scope (a showroom logout would have signed the person out on their phone); `mfa/verify`'s unconfirmed-level 401 left a working `aal2` session behind; the returning-staff gap above; and `logout` answered 200 without clearing anything when no client could be built.
CANON: docs/reference/site.md, scripts/verify.sh. STATE/ROADMAP/DECISIONS wait for T5.
NEXT-NEEDED: none
=== END ===
