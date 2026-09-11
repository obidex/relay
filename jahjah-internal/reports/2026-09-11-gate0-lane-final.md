KIND: final
The ERP chunk lane now respects GATE 0: it never starts a chunk labelled `design` on its own. It
writes one line on the card saying the chunk waits for GATE 0, and the chunk starts only when someone
applies `chunk:approved` by hand. A card now states how many chunks it will have (`chunks planned: N`).
The lane closes the card only when that many exist and all are done; with fewer it says so once on the
card and waits. A card with no such line keeps the old behaviour and says so once. The website lane is
unchanged (its settings print identically before and after). Canon: the strategist's design protocol,
the prompt shape now points at the chunk template, and self-made rules may only tighten a gate (D249).
33 CI fixtures run the real lane logic; 18 of 18 deliberate breakages were caught. Two follow-ups are
registered on D249 for the strategist (a GATE 0 label-only residual and a count-wording residual).

=== RELAY ===
HEAD: d91ebc3 | tree: clean
CI: pass — post-merge main run https://github.com/obidex/jahjah-internal/actions/runs/34638381022 (ci-ok success); PR run 34637584420 green; Vercel production READY for d91ebc3
DONE: PR #159 squash-merged (branch deleted) — lane: design label held, chunks-planned close-out, once-only card notes, WEB_DISPATCH_SELFTEST
DONE: 33 shell-lint chain fixtures, 18/18 mutants killed; website LANE_PRINT_CONFIG byte-identical before/after, ERP gains only L_DESIGN
DONE: installed atomically to /opt/jahjah/web-dispatch — all four lane files equal merged infra/vps; next ERP and website polls ran clean
DONE: canon — STRATEGIST §2 design protocol, §4 pointer + three lines, §7 rules-only-tighten (16206 B); card/chunk templates; automations +2; D249
DONE: `design` label created on jahjah-internal
FILES: 8 — infra/vps/web-dispatch/web-dispatch.sh, README.md, .github/workflows/ci.yml, .github/ISSUE_TEMPLATE/card.md, chunk.md, docs/STRATEGIST.md, docs/DECISIONS.md, docs/runbooks/automations.md
FINDINGS/BLOCKERS: bypass (3 rounds) + test-validity (2 rounds), no blocking finding; all MEDIUMs fixed except two registered D249 follow-ups (GATE 0 label-only residual; count-wording residual) and two cosmetic card-log LOWs — details in PR #159. STRATEGIST §1 "Cards" does not yet mention `design` or `chunks planned` (strategist-owned, not edited)
NEXT-NEEDED: none
=== END ===
LESSON: A review fix that narrows what a parser reads can open the hole it was meant to close — re-run the adversary on the fix, not just the finding.
