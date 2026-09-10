T2 is done: the canon diet merged in #81. The six mirrored canon files went from 262,210 to 70,072 bytes, and all history moved verbatim to `docs/archive/`, which is never mirrored or loaded. The first reviewer pass found one lost rule, the preflight's check of the three Sanity env names. It was restored and the second pass was clean. Codex answered CLEAN in 2m03s, but on the issue-comment surface my poll loop skipped, so I sent one redundant `@codex review`. T3, the chunk close, is next.

=== REPORT: P2b-1c-canon-diet · progress (T2) ===
HEAD: 8e80804 | tree: clean | branch: master (T3 next)
PRs: #81 8e80804 merged (T2)
CI: #81 `ci` green (49s)
PROD: docs-only; Vercel preview passed
DONE:
- File sizes in bytes (before → after): STATE 49,677 → 7,722 · ROADMAP 53,875 → 8,521 · DECISIONS 90,924 → 20,316 · CLAUDE.md 29,754 → 11,994 · STRATEGIST 28,452 → 11,991 · REVIEW.md 5,914 → 661. Every target was met.
- W-numbers: 151 → 151. Open F-ids: 25 of 25 still in ROADMAP. Closed: 37 of 37 in `docs/archive/ROADMAP-closed.md`. Every W cited in CLAUDE/STRATEGIST/AGENTS exists. Reference: no drift.
- The owner's session-efficiency rules are in `CLAUDE.md` §5 and STRATEGIST §1.
- REVIEW.md points at AGENTS.md. AGENTS.md changes only its header sentence.
- CLAUDE.md keeps §3/§4/§7/§9 numbering, because `.claude/**`, `verify.sh` and AGENTS.md cite those sections.
- Reviewer: pass 1 BLOCK (the lost env-name check, plus 4 NOTEs acted on); pass 2 CLEAN.
DEVIATIONS: none from the plan. Judgment calls:
- The Codex rule's "please review" comment is written as `@codex review`, the trigger Codex documents.
- `ROADMAP-closed.md` also carries the BEFORE ROADMAP verbatim, which keeps the done-phase prose.
FINDINGS/BLOCKERS:
- My first 5-minute poll on #81 read reviews, inline comments and reactions, but not issue comments. So it missed Codex's "Review Result: CLEAN" at 23:34:58Z, and one redundant `@codex review` went out at 23:38:22Z (W130's failure mode, no harm).
- `.github/workflows/claude-review.yml`'s header comment still says REVIEW.md carries five always-checks. That file is outside this chunk's Tier-3 list, so T3 opens a row for it.
CANON: STATE, ROADMAP, DECISIONS, STRATEGIST, CLAUDE.md, REVIEW.md, AGENTS.md (header), plus the 3 new archive files
NEXT-NEEDED: none
=== END ===
