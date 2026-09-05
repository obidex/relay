**T3 is merged and F44 is closed: `ci` no longer depends on one product staying published.** The Verify step now receives the two `PUBLIC_SANITY_*` secrets, so the hidden-product guard learns which store to interrogate from the environment instead of having to read it out of the built HTML. The acceptance was checkable in the run's own log and it reads exactly right — `products in ***/*** (via env, agreeing with dist/)`, where the master run an hour earlier read `(via dist/)`.

**This fix has a price and the code now says so out loud.** The guard documents a corner where an asserted store with no witness to corroborate it can report a vacuous pass. That corner was unreachable in CI *precisely because* CI had no environment pair — and it is reachable now. So F44 trades a false red for a possible vacuous green in the same corner. That is the better failure for a gate nobody is allowed to bypass, but it is a real weakening of one corner rather than a pure win, and the comment that used to claim CI was immune has been rewritten to say what replaced it rather than quietly deleted.

=== REPORT: P1.2-gates-deps · progress ===
HEAD: 0eb0a2a | tree: clean | branch: master | git ls-files: 77
Issue: #44 — open, chunk:running

PRs:
  #45 de5a88a MERGED — T1: the amended Arabic gate in every reviewer contract (F42, partial).
  #46 7482dde MERGED — T2: the three indirect Dependabot updates, lockfile only.
  #51 0eb0a2a MERGED — T3: CI's Verify step resolves the store from env (F44).

CI: green on every PR and every post-merge master run.
  PR runs    #45 33963942745 · #46 33964825700 · #51 33965603270
  master     #45 33964022075 · #46 33964933757 · #51 33965711699
  (#45's first run, 33963738894, was RED on the tier3-guard trap reported earlier.)

PROD: READY. Live probes after the T3 merge:
  /  200 · /ar/  200 · /products/  200 · /sitemap-0.xml  200 · /robots.txt  200 · /admin  200

DONE (this report adds T3):
  T3 — #51 merged. ci.yml's Verify step env gains PUBLIC_SANITY_PROJECT_ID and
       PUBLIC_SANITY_DATASET, copied from the Build step; the two env maps are now identical.
       Pure insertion, two lines. Confirmed by parsing the YAML rather than reading the diff:
       EXPECTED_PAGES "67", job name `ci`, triggers, concurrency, permissions and all three
       action SHA pins unchanged.
       scripts/hidden-products-check.mjs is COMMENTS ONLY — every changed line begins with `//`,
       verified mechanically; `node --check` passes; scripts/ is not Tier 3.

ACCEPTANCE, measured in CI's own log:
  PR #51    Verify step: `products in ***/*** (via env, agreeing with dist/)`
  master    same string on the post-merge run 33965711699
  before    master run 33964933757: `products in ***/*** (via dist/)`
  Both values are registered repo secrets, so GitHub masks them job-wide — the log shows ***/***
  and never the store names. The checkable substring is `(via env`.

THE TRADE F44 PAID, stated because it is not a pure win:
  The guard's residual corner — an env pair set with NO witness to corroborate it can report
  `0 hidden (vacuous)` about a build it never examined — was unreachable in CI only because CI had
  no env pair. It is reachable now. Losing the witness costs a vacuous pass instead of a false red.
  Better for a gate nobody can bypass (W100), but a real weakening, and a second product carrying
  images would buy the witness back without paying it.
  NO PREVIOUSLY-GREEN RUN CAN TURN RED: rule 2 needs exactly one secret empty, and an empty
  projectId throws in createClient during the BUILD step, which runs first; rule 3 needs the two
  sources to disagree, and in CI both derive from the same secrets, so agreement is structural.
  ONE NEW RED PATH DOES EXIST and is worth knowing when it fires: a foreign
  cdn.sanity.io/images/<other>/<other>/ URL pasted into content now trips rule 3, where before it
  silently made the check query the wrong store. That is the guard working.

DEVIATIONS this task:
  4. The plan said "Remove the code comment … that says CI does not pass them". Three passages were
     REWRITTEN instead (+19/-14), because deleting the third would have dropped the one-product
     measurement and the trade above with it. In scope, but not the plan's literal verb.
  5. No W number is cited in the new comments. A draft cited W132 twice — the number T4 appends. If
     this chunk had stopped between T3 and T4, master would permanently carry a comment pointing at
     a decision that does not exist. They cite "F44, chunk P1.2" instead. The executor's reviewer
     caught this; it is the class of error W122/W130 are about.
  T1's three deviations stand unchanged.

CODEX — ALL FOUR SURFACES READ (W130):
  #51  👍 at 12:18:52Z, 1m52s after open. No review, no inline, no issue comment. No findings.
  AND A MEASUREMENT WORTH ADDING TO W130: the poll caught the intermediate state. At 12:18:42Z the
  only reaction was 👀 (`eyes`); at 12:19:13Z it was 👍 and the 👀 was gone. So EYES IS A FIFTH MARK
  AND IT MEANS "LOOKING", NOT "REVIEWED". #45 showed the same thing — 👀 at 11:35:27Z, replaced when
  the review with its P2 landed at 11:38:13Z. A session that polls for 👍 and stops at 👀 will
  record a verdict that has not been given; a session that treats 👀 as approval is worse.
  Running tally this chunk: 3 PRs, 3 answered, findings on 1 (#45's P2, fixed and confirmed).

EXECUTOR REVIEWER: one pass on T3 — 0 BLOCK, 1 FIX, 8 NOTE. The FIX was the W132 citation above.
  Four wording notes also applied: a circular cross-reference where each of two paragraphs sent the
  reader to the other; two lines over the file's 100-column convention; an over-claim that the
  residual was "no longer theoretical anywhere", when it was always reachable on a developer clone
  and CI was the exception; and a loose "rule 3 governs" where in fact `envPair ?? fromDist` selects
  the store and rule 3 only guards that choice.

DEPENDABOT: the bot opened FOUR NEW PRs within 90 seconds of the T2 merge, rescanning the refreshed
  lockfile exactly as it did after #37:
    #47 svgo 4.0.1 -> 4.1.0            indirect, minor
    #48 linkify-it 5.0.0 -> 5.0.2      indirect, patch
    #49 sharp 0.34.5 -> 0.35.4         requires an ASTRO MAJOR as ancestor
    #50 astro 6.2.2 -> 7.3.1           direct, MAJOR
  None is in this chunk's named scope and none is touched. #49 and #50 need their own named chunk
  on the F34 pattern. #20 and #21 (Studio 5->6) remain open and untouched.
  Applied and closed this chunk: #38, #39, #40, each "applied in #46 (P1.2)".

NEXT: T4 — canon close. STATE, ROADMAP (F42 partial, F44 closed, F46 STAYS OPEN, F31 notes #38-#40),
  DECISIONS W131-W133, npm run reference, then the final report.

NEXT-NEEDED (strategist) — unchanged:
  1. How "Bash(gh issue close:*)" gets into .claude/settings.json, given the harness classifier
     refuses an interactive session. F46 cannot close until it does, and W133 as the plan drafts it
     would be false.
  2. Whether REVIEW.md should keep the imported "P1" severity label or use 🔴.
  3. Re-issue T1(f)(2) alongside (e).
  4. NEW: a chunk for #49/#50 (astro 7 major, sharp), on the F34 pattern.
=== END ===
