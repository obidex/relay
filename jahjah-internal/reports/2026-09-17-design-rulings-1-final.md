KIND: final
Your design rulings are now in the rulebook as D251 and D252, with your words quoted exactly. D251 records five rulings:
- The app is light.
- Toasts, side drawers and "denied" panels are approved.
- Typed prices within a floor, and numbered revisions after confirm, are approved, each behind its own database change.
- Pattern screens are approved as one batch.
- The aging buckets, "no reliability signal" and "no converter" stay as they are.

D252 makes the SYP rate a daily rate set by a manager, which a cashier can no longer type. Nothing is built yet, and the live payment form still takes a typed rate until that database change lands. The review found that today's order form already accepts a typed price and does not record price changes, so "everything recorded in the audit" becomes true only once the price-floor change lands. Three questions are left for you, below.

=== RELAY ===
HEAD: c5eef66 | tree: clean
CI: pass. The post-merge main run 35235149612 is green (ci-ok, E2E): https://github.com/obidex/jahjah-internal/actions/runs/35235149612. PR #169 run 35233804866 was green. Vercel production is READY for c5eef66.
DONE:
- card design-rulings opened as #167 (card, card:approved, chunks planned: 1) with chunk #168 opened chunk:running, now chunk:done
- card #156 has "chunks planned: 3" and lists #163
- D251 and D252 appended, with owner quotes verbatim
- CLAUDE.md §3 theme and §4 rate updated
- SPEC: §0.1 money bullet, new §0.2 capability list, §2.1, §6.1, §6.2, §6.6, §6.7, §7, §8
- ROADMAP row collapsed; the file is 470 B under its cap
- STRATEGIST §2, §4, §6, §7
- money-fx D252 rule
- STATE next step
- PR #169 merged (c5eef66)
FILES: 7. CLAUDE.md, docs/DECISIONS.md, docs/rebuild/SPEC.md, docs/ROADMAP.md, docs/STRATEGIST.md, docs/STATE.md, docs/pitfalls/money-fx.md
FINDINGS/BLOCKERS:
(1) Contradictions in the prompt:
  - The scope line omits docs/pitfalls/money-fx.md, which edit F requires. I followed F.
  - D252 says "D060 stands" but creates a rate table. It now says it carves one table out of D060, and D060 carries a marker.
  - D250's "rate today / never a day rate" clause and SPEC §2.1/§7/§8 still said "cashier-typed". All are now aligned or marked.
  - SPEC §6.2 listed Toast as not adopted. It is now adopted, and its package needs owner approval under CLAUDE.md §11D.
  - SPEC §6.6 still redirected a denied route. It now points to the Denied row.
(2) Caps forced trims of text that was only duplicated elsewhere: the CLAUDE.md header, the §3 kit bullet and §13.15; the ROADMAP §3/§4 intros; the STRATEGIST Cards mechanics. Now CLAUDE.md is 18408/18432 and STRATEGIST 16378/16384, with almost no headroom.
(3) Review, none at the D028 bar. The compliance and bypass seats each went several rounds and ended clean.
  - Tighten-only guards were added to D251 and D252.
  - D252's database enforcement is stated as a rule, with the mechanics in SPEC §0.2 (off the mirror).
  - Supersession markers were added to D060, D061, D135, D248 and D250.
(4) Premise found in review: today's draft-order flow already accepts a typed line price and keeps no price audit. The price-floor chunk in SPEC §0.2 is what closes it.
(5) The prompt's D251 status line cited only STRATEGIST §4. It now cites §2, §4 and §7, where D251's content also landed.
NEXT-NEEDED: Owner calls before the D252 migration's GATE 1 and the next GATE 0:
  (a) How is a corrected SYP receipt rated: the original receipt's rate, today's rate, or an approved override?
  (b) Do the D251(4) quotes confirm "options only where a pattern is new", which narrows D248's 2–4 options?
  (c) Which business timezone applies to the daily rate and the 10:00 alert?
=== END ===
LESSON: A LOCKED money rule written as target state needs its database-enforcement invariants in the same entry — otherwise each fix opens the next bypass.
