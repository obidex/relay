DONE. P2b-1c is closed in three merged PRs. The grouped security update is applied, and `npm audit` went from 16 to 11. The canon every session loads went from 262 KB to 70 KB with no rule lost, and all history is archived verbatim. The owner's session-efficiency rules are now in `CLAUDE.md` and STRATEGIST. The next step is still P2b-2, which waits on the owner at a PC (Supabase #2, the write token, Vercel Pro).

=== REPORT: P2b-1c-canon-diet · done ===
HEAD: cbc423a | tree: clean | branch: master
PRs:
- #80 5cd1e40 merged (T1: npm-security group; Dependabot #77 closed naming it)
- #81 8e80804 merged (T2: the canon diet)
- #82 cbc423a merged (T3: chunk close)
CI: master `ci` green on cbc423a (run 34543548002)
PROD: Vercel production for cbc423a is READY. Live `/`, `/ar/`, `/products/`, `/about/` all return 200 (4/4).
DONE:
- T0: labels moved (chunk:running, then chunk:proposed removed).
- T1: lockfile only.
  - It is byte-identical to the bot's, and the 29 `libc` fields were restored. tsx moved too: it pinned esbuild.
  - All 67 public HTML pages are byte-identical. Only the Studio bundle changed.
  - `npm audit` 16 → 11.
- T2: the mirrored canon went from 262,210 to 70,072 bytes.
  - STATE 7.7 KB · ROADMAP 8.5 KB · DECISIONS 20.3 KB · CLAUDE.md 12.0 KB · STRATEGIST 12.0 KB · REVIEW.md 661 B.
  - 151 of 151 W-numbers kept. All 25 open F-rows are still in ROADMAP, and all 37 closed ones are in the archive.
- T3: F32, F57, F61 (ratified) and F62 closed; F63 opened; W152 written.
  - `dependabot.yml` comments reworded, with 0 non-comment lines changed.
- Codex:
  - #80: 👍 in 1m51s.
  - #81: issue comment "CLEAN" in 2m03s.
  - #82: 👍 in 2m19s.
- Reviewer passes:
  - T1: CLEAN.
  - T2: BLOCK (one lost rule), then CLEAN after the fix.
  - T3: CLEAN.
DEVIATIONS: none from the plan. Two judgment calls:
- The Codex rule's "please review" comment is written as `@codex review`.
- `ROADMAP-closed.md` also keeps the BEFORE ROADMAP verbatim.
FINDINGS/BLOCKERS:
- On #81 my first poll read three Codex surfaces, not four (issue comments missed). It sent one redundant `@codex review` after Codex had already answered CLEAN (W130's failure mode). #82's poll read all four.
- F63: `claude-review.yml`'s header still describes REVIEW.md's old five checks. That file was not on this chunk's Tier-3 list.
- `CLAUDE.md` and STRATEGIST sit within about 10 bytes of their 12 KB targets. Any addition needs an equal trim.
- Dependabot queued a rescan on cbc423a. Any PR it opens belongs to the next dependency task (W114).
CANON: STATE, ROADMAP, DECISIONS (W152), STRATEGIST, CLAUDE.md, REVIEW.md, AGENTS.md (header), docs/archive/{STATE-history,ROADMAP-closed,DECISIONS-full}.md, .github/dependabot.yml (comments)
NEXT-NEEDED: none from this chunk. P2b-2 needs the owner's three PC steps.
=== END ===

Handover: the canon is now small enough to read whole at every session start. The next step is P2b-2, because the code side has been ready since P2b-1. Pressure-test its Admin Mode data model against how Shopify's admin handles SKU-level prices per customer tier.
