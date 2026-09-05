**T1 is merged, and it did not go entirely to plan — two of its eight items are not in it, both reported rather than worked around.** The amended Arabic gate (W125) is now stated in every file a reviewer actually reads, so the executor's own reviewer will stop blocking strings the owner said may ship, and Codex will stop raising a correct P1 against a rule that was superseded on 2026-09-04. That is most of F42 closed.

**What did not happen: the one-line allowlist addition.** `"Bash(gh issue close:*)"` could not be written to `.claude/settings.json` — the executor harness refuses every route to that file, in an interactive session, which is the session type CLAUDE.md §9 says is the fix for an allowlist gap. So **F46 stays open**, and the thing it guards — `/relay-report final` closing this issue by itself — is not proven by this chunk. The chunk's own final report will say plainly what happened when it tries.

**Codex found a real defect in its own contract and it is fixed.** It read the new reviewer rule and pointed out that `/ship` demands `REVIEW: CLEAN` at step 3 while the PR body it referred to does not exist until step 5 — so a task carrying approved Arabic could have deadlocked on an unsatisfiable finding. Fixed in a second commit, answered in-thread, and Codex re-reviewed and confirmed it.

=== REPORT: P1.2-gates-deps · progress ===
HEAD: de5a88a | tree: clean | branch: master | git ls-files: 77
Issue: #44 — open, chunk:running

PRs:
  #45 de5a88a MERGED — T1: the amended Arabic gate in every reviewer contract (F42, partial).

CI: green.
  PR run  33963942745 (green)   earlier 33963775439 (green)   first attempt 33963738894 (RED, see below)
  master  33964022075 (green)

PROD: dpl_FYA4nJScgVbtYcF6He5Hetv2arpr READY production 03d6cd9 at report time; the de5a88a
  deployment follows the merge. Live probes taken before the merge:
  /  200 · /ar/  200 · /products/  200 · /sitemap-0.xml  200 · /robots.txt  200 · /admin  200

DONE:
  T0 — Labels moved by this session: chunk:running added and confirmed, then chunk:proposed
       removed. Two commands, add before remove. chunk:approved never touched. The issue already
       carried chunk:running from the dead run; both commands were still run.
  T1 — #45 merged. AGENTS.md and REVIEW.md carry the amended BAR clause and an item 4 that is
       byte-identical between them (756 chars, verified unwrapped). .claude/agents/reviewer.md
       item 7, .claude/skills/ship/SKILL.md step 4, docs/STRATEGIST.md §4 RULES and CLAUDE.md §6
       all state the amended gate. The relay-report skill's example body path is now
       /opt/jahjah/web/.astro/relay-report.md.

DEVIATIONS — three, all deliberate:
  1. T1(e) NOT APPLIED — REFUSED. The one-line addition of "Bash(gh issue close:*)" to
     .claude/settings.json was attempted twice, by a script and by the file-editing tool. Both
     returned verbatim:
         Permission for this action was denied by the Claude Code auto mode classifier.
         Reason: Blocked by classifier.
     T1's own contingency says split it out, leave it uncommitted, report BLOCKED, do not route
     around it. settings.json is byte-identical to master. THIS IS AN INTERACTIVE SESSION AND THE
     REFUSAL WAS STILL ABSOLUTE — it is not W116's dispatched-session limitation, so CLAUDE.md §9's
     "an allowlist gap is fixed by an interactive session" no longer holds unaided. Needs the
     strategist.
  2. T1(f) change (2) NOT APPLIED, on purpose. The plan authorised deleting the skill paragraph
     saying gh issue close is not yet allowlisted, justified by "(it is, after this PR)".
     Deviation 1 makes that false. The paragraph is still true and still load-bearing for a
     dispatched session, so it stays. Re-issue (f)(2) together with (e).
  3. A third edit inside T1(f), named because GATE 2 authorised "the two changes in T1f": change
     (1) also rewrote the same paragraph's second half, which said the report body "dirties the
     tree the next preflight requires clean" — directly contradicting the new instruction to write
     it into gitignored .astro/. Verified: .gitignore:4, and git check-ignore agrees.
  Plus one amendment to prescribed text, under the plan's own instruction to treat a Codex wording
  finding on this contract as real: reviewer.md item 7's list clause is now a NOTE at ship step 3
  and a FIX only on a re-review of an open PR. The blocking half — "a string the chunk plan did not
  approve" — is unchanged and was always evaluable before a PR exists.

CODEX — ALL FOUR SURFACES READ (W130):
  #45  review 11:38:13Z (2m48s after open)  ONE inline P2 on .claude/agents/reviewer.md:30
       reaction 11:35:27Z was an EYES, not a thumbs-up — an acknowledgement, not a verdict; it
       cleared when the review landed. Worth adding to the four-surface note: 👀 is a fifth mark
       and it means "looking", so a session polling for 👍 must not read it as one.
       Finding: /ship requires REVIEW: CLEAN at step 3 but gh pr create is step 5, so a FIX on a
       missing PR-body list is unsatisfiable and could deadlock a task carrying approved Arabic.
       CORRECT. Fixed in 75a7f6d, answered in-thread, and Codex posted a follow-up review at
       11:41:16Z confirming the fix and stating "I have no further findings."
  Silence on nothing.

EXECUTOR REVIEWER: one pass on T1 — 0 BLOCK, 1 FIX, 1 NIT, 8 NOTE. The NIT was applied. The FIX
  (REVIEW.md imports the severity label "P1", which that file defines nowhere — its vocabulary is
  🔴/nit) was shipped as-is deliberately: the plan requires T1(b) to be the "same wording" as T1(a)
  and requires the two files to agree, so deviating would break both constraints to fix a mismatch
  that reads unambiguously in context. FLAGGED FOR THE STRATEGIST TO RULE.

MEASURED, AND WORTH CANON: THE TIER-3 AUTHORIZATION LINE MUST NOT BE MARKDOWN-EMPHASISED.
  The first ci run failed in 7 s. tier3-guard matches ^Tier-3: authorized by chunk .+ anchored at
  line start, and the body carried the line wrapped in ** ** for emphasis, so the line began "**".
  Fixing the body re-ran the guard and it passed — no empty commit needed, as W101 says. This is a
  cheap trap and nothing in canon warns about it.

DEPENDABOT: untouched this task. #38, #39, #40 open (T2's). #20, #21 open, untouched (F34).

NEXT: T2 — the three indirect Dependabot updates (#38 brace-expansion, #39 tar, #40 postcss),
  lockfile only, with the libc guard from W123. Then T3 (ci.yml Verify step gets PUBLIC_SANITY_*),
  then T4 (canon close).

NEXT-NEEDED (strategist):
  1. How "Bash(gh issue close:*)" gets into .claude/settings.json, given an interactive session is
     refused. F46 cannot close until it does, and W133 as drafted would be false.
  2. Whether REVIEW.md should keep the imported "P1" label or use 🔴.
  3. Re-issue T1(f)(2) alongside (e).
=== END ===
