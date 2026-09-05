**P1.2 is done. Four pull requests, all merged, all green — and the one thing it could not do is the thing worth your attention.**

The Arabic amendment you made on 2026-09-04 now governs every reviewer that actually fires. Until this morning, two live gates still told reviewers to block Arabic you had already released: the file Codex reads, and the executor's own reviewer, which listed an unmarked Arabic string as a *blocking* finding. Both are corrected, and the wording is identical in both places so there is one contract rather than two. The three dependency updates are applied and the bot's pull requests closed; `npm audit` went 24 → 21 and **the critical vulnerability is gone**. And CI's hidden-product guard now learns which store to check from its own configuration, so publishing or unpublishing a product can no longer turn the build red for a reason nobody could see.

**What failed: `gh issue close` is still not allowlisted, and the reason is worse than the missing line.** The chunk ran interactively *specifically* to add it — our own rules say a dispatched session cannot edit that file and an interactive one can. The interactive session was refused too. So this repository currently has **no route at all** to its own permission list, and that is now a high-priority finding of its own (F47), because any future plan whose fix is "add an allow rule" will stop in exactly the same place. It needs you: editing `.claude/settings.json` by hand is the only route anyone has.

**And then the close worked anyway, which nobody predicted.** This report ran `gh issue close` on #44 and it succeeded — exit 0, no refusal, no prompt reaching the session — even though the rule is still absent from the allow list. So the *practical* failure F46 forecast did not happen here: in an interactive auto-mode session the classifier permits the command on its own judgement. What that does **not** establish is the dispatched case, which is the one F46 was actually written about and which remains untested. Read this as good news with a caveat rather than as F46 being closed.

**One more thing you should know, because it is the third time in three chunks.** The chunk-close review found sixteen factual errors in my own canon draft, two of them serious enough to block — including a Codex response time I published as 3m11s that was really 1m02s, and a stray blank line that would have made two new register rows render as unreadable raw text. I had already caught two others myself by re-measuring. The rule that catches these (W122: re-measure a claim at the moment you write it down) is now three-for-three, and I have added the specific trap that produced today's error.

=== REPORT: P1.2-gates-deps · done ===
HEAD: 3c8d03b | tree: clean | branch: master | git ls-files: 77
Issue: #44 — CLOSED by this report (W127). `gh issue close` exited 0 with no refusal and no
prompt reaching the session, despite the allow rule still being absent — see F46 MEASURED below.

PRs:
  #45 de5a88a MERGED — T1: the amended Arabic gate in every reviewer contract (F42).
  #46 7482dde MERGED — T2: the three indirect Dependabot updates, lockfile only.
  #51 0eb0a2a MERGED — T3: CI's Verify step resolves the store from env (F44).
  #52 3c8d03b MERGED — T4: canon close.
  All four merged with --subject, so every master commit carries its own (#n) (W129).

CI: green on every PR and every post-merge master run.
  PR runs    #45 33963942745 · #46 33964825700 · #51 33965603270 · #52 33966734672
  master     #45 33964022075 · #46 33964933757 · #51 33965711699 · #52 33966930367
  #45's FIRST run, 33963738894, was RED in 7 s — see MEASURED below.

PROD: dpl_HkTEmXSJ9V7b4YsYzXmN1PF3kmMD READY production 3c8d03b  ← current
Live probes after the final merge:
  /  200 · /ar/  200 · /products/  200 · /sitemap-0.xml  200 · /robots.txt  200 · /admin  200

DONE:
  T0 — Labels moved by this session (no dispatcher): chunk:running added and confirmed, then
       chunk:proposed removed, two commands, add before remove. chunk:approved NEVER added.
  T1 — #45. AGENTS.md, REVIEW.md, .claude/agents/reviewer.md, .claude/skills/ship/SKILL.md,
       docs/STRATEGIST.md §4 and CLAUDE.md §6 all state the amended gate. AGENTS.md's and
       REVIEW.md's item 4 are byte-identical (756 chars, verified unwrapped). F42 CLOSED.
  T2 — #46. brace-expansion 2.1.0->2.1.4 and 5.0.6->5.0.9, tar 7.5.15->7.5.22, postcss
       8.5.14->8.5.28. 14 changed lines and an order-preserving JSON walk confirms no fifteenth.
       package.json byte-identical to master. npm audit 24 (1 critical/15 high/6 mod/2 low) -> 21
       (0/13/6/2); resolved brace-expansion, postcss, tar; none new. npm stripped all 29 libc
       fields again and they were restored again (W123). Built output proved unchanged: builds
       from both lockfiles give 104 byte-identical files in dist/.
  T3 — #51. ci.yml's Verify step env now matches the Build step's. F44 CLOSED.
  T4 — #52. STATE, ROADMAP, DECISIONS W131-W134, reference regenerated (no drift).

BLOCKED — ONE ITEM, AND IT NEEDS YOU:
  T1(e), the one-line addition of "Bash(gh issue close:*)" to .claude/settings.json, is REFUSED.
  Attempted twice, by a python script and by the Edit file tool. Both returned verbatim:
      Permission for this action was denied by the Claude Code auto mode classifier.
      Reason: Blocked by classifier.
  T1's contingency was followed exactly: split out, left uncommitted, reported, NOT routed around.
  settings.json is byte-identical to master.
  WHY IT MATTERS MORE THAN THE MISSING LINE: CLAUDE.md §9 and W116 both say an allowlist gap is
  repaired by an INTERACTIVE session. This chunk ran interactively for that express purpose and was
  refused anyway. The remedy the canon prescribes does not work. -> ROADMAP F47 (high), W133.
  NOT ESTABLISHED, and not guessed at: edits to .claude/agents/reviewer.md and both skills SUCCEEDED
  in the same session, so the boundary is narrower than all of .claude/** and nobody has mapped it.
  CONSEQUENCE ALSO HONOURED: T1(f)(2) would have deleted the skill paragraph warning about this very
  refusal, justified by "(it is, after this PR)". That justification became false, so the deletion
  was withheld. A cleanup authorized on the strength of a change that did not land must not land.

F46 MEASURED, AND IT IS NOT WHAT THE ROW PREDICTED:
  `gh issue close 44 -R obidex/jahjah-website --comment "closed by /relay-report final"` RAN, exit 0,
  no refusal and no permission prompt reaching this session — with "Bash(gh issue close:*)" still
  ABSENT from the allow list. T4's acceptance was "this final must close the issue WITHOUT a
  permission prompt"; that is MET, but not for the reason the plan assumed. The allow list did not
  authorize it; the auto-mode classifier did, on its own judgement.
  WHAT THIS DOES NOT ESTABLISH, and the distinction is the whole of F46: a DISPATCHED session runs
  against the allowlist rather than this classifier, and that case is still untested. F46's real
  question — can the lane's own executor close its issue — is unanswered, not answered.
  SO F46 STAYS OPEN, and its acceptance is unchanged: the rule in the allow list, exercised. But its
  urgency drops, because the interactive lane — the only one that has ever finished a chunk — is not
  blocked by it. The row and W133 in this chunk's canon were written BEFORE this measurement and say
  the close was untried; they are not wrong, but they are now incomplete. Next chunk should fold this
  in, and the cheapest real test is a dispatched smoke chunk whose only job is to close its own issue.

FINDINGS REGISTER: F42 CLOSED (#45) · F44 CLOSED (#51) · F46 STILL OPEN · F47 NEW (high — no route
  to the allow list) · F48 NEW (med — #49 sharp + #50 astro 6->7 are majors and travel together;
  they need their own named chunk on the F34 pattern) · F43 unchanged at four strings, because no
  Arabic string shipped: git log 03d6cd9..HEAD -- src/ is EMPTY.

DEPENDABOT DISPOSITION:
  #38 brace-expansion  CLOSED "applied in #46 (P1.2)"
  #39 tar              CLOSED "applied in #46 (P1.2)"
  #40 postcss          CLOSED "applied in #46 (P1.2)"
  #20 sanity 5->6      OPEN, untouched (F34)      #47 svgo        OPEN, indirect minor (F48)
  #21 vision 5->6      OPEN, untouched (F34)      #48 linkify-it  OPEN, indirect patch (F48)
                                                  #49 sharp       OPEN, MAJOR via astro (F48)
                                                  #50 astro 6->7  OPEN, MAJOR (F48)
  The bot opened #47-#50 between 69 and 105 seconds after #46 merged, rescanning the new lockfile
  exactly as it did after #37.

CODEX — ALL FOUR SURFACES READ ON ALL FOUR PRs (W130). 4 of 4 answered, findings on 1.
  #45  review + inline P2 in 2m53s.  #46  👍 in 1m02s.
  #51  👍 in 1m50s.                  #52  👍 in 3m25s.
  #45's P2 was CORRECT and against its own newly-amended contract: the executor's reviewer runs at
  /ship step 3, which requires REVIEW: CLEAN, while gh pr create is step 5 — so a FIX demanding a
  PR-body list was unsatisfiable and could deadlock a task carrying approved Arabic. Fixed in
  75a7f6d, answered in-thread, and Codex posted a follow-up review confirming it and stating
  "I have no further findings". A PR with findings carried no 👍 and the three without findings each
  carried one, which is W130's complementarity holding for a fourth chunk.

MEASURED THIS CHUNK, now canon (W134):
  (a) THE TIER-3 AUTHORIZATION LINE MUST NOT BE MARKDOWN-EMPHASISED. tier3-guard matches
      ^Tier-3: authorized by chunk .+ anchored at line start. #45's body had it wrapped in ** **, so
      the line began "**" and the guard failed the PR in 7 seconds — and its error message, "add a
      line reading exactly …", does not hint that the line IS there and merely decorated. Editing
      the body re-ran the guard, no empty commit needed.
  (b) CODEX HAS A FIFTH MARK: 👀 means "LOOKING", NOT A VERDICT. Seen on all four PRs within seconds
      of opening; on #52 it persisted across four polls (~2 min) before flipping to 👍. A poll that
      stops at "a reaction exists" records a verdict never given. Match on content == "+1".
  (c) COMPUTE AN INTERVAL FROM THE ARTEFACT'S TIMESTAMPS, NOT YOUR POLLING LOOP'S WALL CLOCK. Both
      progress reports in this chunk published a wrong Codex response time. #46's went out as 3m11s;
      createdAt 12:00:23Z against the reaction's 12:01:25Z makes it 1m02s — a threefold error, in the
      direction that makes the external system look slower. THE FIGURES IN THIS REPORT AND IN CANON
      ARE THE RE-MEASURED ONES; the two progress reports on this issue and in the relay are wrong and
      are not being edited, because the correction is the more useful record.

EXECUTOR REVIEWER: four passes.
  T1  0 BLOCK, 1 FIX, 1 NIT, 8 NOTE — nit applied; the FIX flagged for you (below).
  T2  0 BLOCK, 0 FIX, 3 NOTE — changed no shipped byte, but re-derived the lockfile diff
      independently, walked every dependency range, built the site TWICE to prove dist/ byte-
      identical, and corrected a factual error in my PR body before it shipped.
  T3  0 BLOCK, 1 FIX, 8 NOTE — the FIX caught a citation of W132 BEFORE W132 EXISTED. Had the chunk
      died between T3 and T4, master would permanently carry a code comment pointing at a decision
      that was never written.
  T4  2 BLOCK, 11 FIX, 3 NIT — the chunk-close fact-check, and W122 earned its keep for the third
      chunk running. Every finding was a factual error, not taste: the 3m11s/1m02s interval; "an
      hour earlier" for a 14-minute gap; "within 90 s" for 69-105 s; A BLANK LINE between the F46
      and F47 rows that under GFM ends the table, so two new rows — one of them high — would have
      rendered as raw pipe-text in the canon and the project-knowledge sync; "Nothing from P1 or
      P1.2 is owed" in a document whose own ledger row calls an unclosed high finding the headline;
      F26 credited to "three chunks through the lane" when P1.2 never carried chunk:approved; and my
      F42 closure over-crediting #45, when the role table was amended in 03d6cd9 and the "P5 line"
      F42 names has never existed in that file. All addressed.

DEVIATIONS — five, all deliberate, all reported when they happened:
  1. T1(e) not applied — refused. See BLOCKED.
  2. T1(f)(2) not applied — its justification died with (e).
  3. A third edit inside T1(f): change (1) also rewrote the paragraph's second half, which claimed
     the report body "dirties the tree" — false once the path moved to gitignored .astro/.
  4. T3 rewrote three comment passages rather than removing one, because deleting the third would
     have dropped the one-product measurement and the trade F44 pays.
  5. T3's comments cite "F44, chunk P1.2" and no W number, for the reason T3's reviewer gave above.
  Plus one amendment to text the plan prescribed verbatim: reviewer.md item 7, under the plan's own
  instruction to treat a Codex wording finding on this contract as real.

NEXT-NEEDED (yours, or the strategist's):
  1. **A route to .claude/settings.json.** You edit it by hand, or the classifier is configured to
     permit it. F46 and F47 both wait on this, and F47 blocks any future plan that needs a new
     allow rule. Two live files still state the disproved remedy — CLAUDE.md §9 and the relay-report
     skill — and neither was P1.2's to change.
  2. Whether REVIEW.md should keep the imported severity label "P1" or use 🔴. The plan required
     AGENTS.md and REVIEW.md to carry identical wording, so it shipped verbatim and is flagged
     rather than silently diverged from. Strategist's call.
  3. Re-issue T1(f)(2) alongside (e).
  4. A named chunk for #49 + #50 (sharp needs the astro 6->7 major as its ancestor, so they are one
     piece of work). Astro majors move astro.config.mjs and the adapter, both Tier 3.
  5. VERCEL PRO (~$20/mo) — unchanged, and still P2's first decision (W090).

HANDOVER: **P2 next — identity and foundation. Vercel Pro first.** The dispatched lane is still
unfixed: checked 2026-09-05 against jahjah-internal's reports index, the run-chunk.sh change W128
asks for has NOT landed, so approve P2's issue only if someone is there to run it interactively.
P1.2 itself ran interactively by design rather than by failure — it edits .claude/** — and the
interactive lane worked cleanly: four PRs, four merges, no session loss.
=== END ===
