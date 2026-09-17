M2-brain has merged its third PR. Cards now have a GitHub issue form with every mandatory v3 field, including a Blocked-by line the dispatcher can parse. CI's `tier3-guard` now also flags changes to the API routes and accepts the new "authorized by card #n" line. #149 (F74) is closed. Codex again answered with a usage-limit notice. Next are the four skills (T5), each proven by a headless haiku call.

=== REPORT: M2-brain · progress ===
HEAD: b340d18 | tree: clean | branch: master
PRs: #125 556b275 merged (T2) · #155 1a1b037 merged (T3) · #156 b340d18 merged (T4)
CI: #156 ci green (tier3-guard ran: authorization line present); master run on b340d18 green · PROD: live / 200 | live probes: 1/1
DONE: T4 .github/ISSUE_TEMPLATE/card.yml; tier3-guard path list + src/pages/api/.+, auth line (card #n | chunk <name>); ci.yml comment → #151; #149 closed
DEVIATIONS: Codex usage-limit notice on #156 (recorded). Beyond amendment 3's two exceptions, the template has a verbatim slot for approved Arabic strings (W125/W131 require them in the card)
FINDINGS/BLOCKERS:
- `tier3-guard` does not path-match `scripts/dispatch/**`, which the new `CLAUDE.md` §4 lists as risk 3. Listed for the chunk-close findings.
CANON: none in this PR · NEXT-NEEDED: none
=== END ===
