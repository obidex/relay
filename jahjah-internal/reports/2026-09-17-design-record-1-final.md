KIND: final
The design you approved on the canvas is now written into the project's rulebook: the tokens and screen patterns (SPEC §0.1), the build order that follows the design (sales second), the nine new sales capabilities as future database changes, the next step (design step D5, inventory), and decision D250. The canon-cleanup PR #161 is merged, its chunk is closed, and the follow-up size cut is opened as #163 but not started. Some of the approved boards conflict with older locked rules; the biggest is the light "Warm Ledger" theme against the dark-first house style. D250 changes nothing live: every such conflict waits for your quoted decision, and the UI chunk it touches stays closed until then. One process change: this chunk's issue was never labelled "approved", because the dispatch lane would have started a second copy of it in the same folder within two minutes.

=== RELAY ===
HEAD: 89d40f0 | tree: clean
CI: pass. Post-merge main run 35216804346 for #166: ci-ok green, E2E green. Post-merge main run 35212444206 for #161: green. Vercel production READY for both.
DONE: #161 merged (8ba18d9) · #158 moved chunk:blocked → chunk:done, decision A recorded on it · chunk canon-lean-B 3 opened as #163 under card #156 (chunk:proposed, model:opus), not run · card design-record opened as #164 (card, card:approved, chunks planned: 1), chunk #165 attached as its sub-issue · PR #166 merged (89d40f0): SPEC §0.1 + §7 order line, ROADMAP §3 row D250, STATE 2026-09-17, DECISIONS D250 · #165 moved to chunk:done · card #164 closed
FILES: 4. docs/rebuild/SPEC.md, docs/ROADMAP.md, docs/STATE.md, docs/DECISIONS.md
FINDINGS/BLOCKERS:
(1) #165 was labelled chunk:running, not chunk:approved as the prompt asked. The ERP lane is active and would have dispatched a second session on this chunk into the same working tree.
(2) There is no "cards skill". I used STRATEGIST §1 Cards, .github/ISSUE_TEMPLATE/card.md and chunk.md, and D244/D249 instead.
(3) ROADMAP had 287 B free and the new row needed ~590 B. To fit, I compressed the §2 rebuild paragraph and Phase L paragraph; the audit detail now points to SPEC §3–§4, and the stale chunk order was removed. ROADMAP is now 14325/14336 bytes, with no headroom left.
(4) SPEC §0.1 is applied verbatim, except `<details>` is in backticks so GitHub does not collapse the rest of the file.
(5) The boards conflict with existing canon in several places. For each, D250 keeps the canon in force and holds the UI chunk it touches until an entry quoting the owner resolves it:
  - Warm Ledger (light) vs D135 dark-first, SPEC §6.1 and CLAUDE.md §3's dark canvas
  - toast, side-drawer edits and the on-page denied state vs SPEC §6.2, §6.7 and §6.6
  - typed prices and confirmed-order edits vs SPEC §2.1 and §7
  - the aging-bucket labels vs D071
  - the spike signal vs Q3-A
  - SYP change and split tender vs D060/D061
(6) Two parts of the method loosen GATE 0 (D248): "options only where a pattern is new" and "approved by exception". D250 suspends both until the owner's confirmation is quoted.
(7) SPEC §7's common row still says "no migration". D250 makes each registered capability its own migration chunk. Still with no register row: WhatsApp sending, per-user preferences, branch colours, the automatic 90+ hold, the one-step cash sale and return inspection.
(8) Review, none at the D028 bar:
  - compliance-reviewer: clean.
  - bypass-reviewer: five rounds; rounds 2–5 corrected my own D250 wording.
(9) Editing the PR body while CI ran cancelled in-flight runs, because the edited event shares the concurrency group. That cost three re-runs.
(10) Card #156 has no "chunks planned:" line, so its close-out counts the 3 sub-issues present.
NEXT-NEEDED: Before chunk 1 of "UI rebuild" is opened, the strategist writes the DECISIONS entry that quotes the owner's rulings on the D250 conflicts (the theme vs D135, the SPEC §6 patterns) and on the two GATE 0 loosenings. If the light theme wins, CLAUDE.md §3's "dark canvas" line changes with it.
=== END ===
LESSON: An interactive session never labels its own chunk chunk:approved — the lane dispatches it within two minutes into the same tree; use chunk:running.
