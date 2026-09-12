P3-B1 T3 is merged: the owner now has three commands for staff accounts — add, reset the authenticator, remove — all run from the VPS with the service key, printing counts and codes and never a password or a key. The interesting part was not the code: Codex questioned the claim that an MFA reset signs the account out, and measuring it against the live project showed the claim is false, which means W167's recovery design needs correcting in T5. A mistyped address could also have turned a customer into staff and deleted their record silently; adopting an existing account now has to be asked for with `--adopt`. Nothing is left in the database: every test account was created and deleted inside the task. T4's end-to-end smoke is next.

=== REPORT: P3-B1-staff-session · progress ===
HEAD: e467302 | tree: clean | branch: master
PRs: #105 24bfda5 merged · #106 fea51bf merged · #107 e467302 merged — the three admin scripts
CI: ci pass on master (fea51bf, T2) · PROD: deployment success, 5/5 live probes | T3 touches no route or page, so nothing on the site changed
DONE: T0 labels · T1 session library · T2 the five routes · T3 staff-add, staff-mfa-reset, staff-remove
DEVIATIONS:
- `staff-add` gained an `--adopt` flag, required when the address already has an account. W080 says a destructive action confirms, and adoption grants a role to an account somebody already has the password to AND deletes its customer record.
- `staff-remove` and `staff-mfa-reset` refuse any account without a `staff` row. From P4 most accounts are customers, and a mistyped address must not cost one their record or their authenticator.
FINDINGS:
- **W167's recovery sentence is wrong, measured today.** It says the admin `deleteFactor` "logs every session out". Against the live project, with a real staff account driven to `aal2` over HTTP: after the reset, `GET /api/staff/me` still answered 200 `aal2`; the refresh token was not revoked (a renewal returned HTTP 200); and the renewed token came back at `aal1`. An admin password change did not end the session either — only deleting the account did. So an MFA reset does not sign sessions out: staff WRITES stop within the access-token lifetime because they need `aal2`, while the session itself continues. A lost device in someone else's hands needs `staff-remove` and a re-add. Corrected in T5, with a register row for the residual.
- Atomicity has a ceiling here: `staff-add` makes two writes with no transaction, because an RPC would need a migration and this chunk has none. The customer row is deleted first and restored if the grant then fails, so neither outcome is silent, but the two writes can still be interrupted between them.
- Codex raised six P2s across the two PRs and every one was a real defect; the last round on #107 was silent after the 5-minute wait and one `@codex review` comment, so the silence is recorded and the merge went on green CI and a clean reviewer.
CANON: docs/reference/site.md. STATE/ROADMAP/DECISIONS wait for T5.
NEXT-NEEDED: none
=== END ===
