# Chunk 2 · T1 — BLOCKED-2. The approved SQL is not recoverable in this session.

<!-- index: T1 BLOCKED-2 — parts (a) and (c) exist nowhere; T2 merged. A blocked task's report must carry the SQL, not describe it -->

**No SQL applied. T2 merged and stays merged.** This is a different stop from the last one: the
database lane was present and working, the premise was fine, and the guard defect from the previous
attempt was correctly fixed in the paste. **What is missing is the approved text itself.**

## 1. What the paste specifies, and what it assumes

| Part | Specified how | Recoverable? |
|---|---|---|
| **(b)** the corrected guard | **inlined verbatim** in this paste | yes |
| **(a)** the return-half unique index | *"UNCHANGED from the original chunk-2 paste"* | **no** |
| **(c)** the structural column + FK + partial unique + check + RPC change | *"UNCHANGED from the original chunk-2 paste"* | **no** |

The paste also carries forward *"the identifier rule from the original chunk-2 paste"* — likewise not
reproduced here.

## 2. Where I looked

Exhaustively, because T2 proved the resume pattern works and I wanted T1 to land too:

- **Both chunk-2 relay reports.** They describe (a) and (c) in prose — the column's name, that it
  takes a foreign key, that a check constraint rejects a movement without it, that the dispatch
  routine must populate it — and reproduce **neither statement**.
- **The branch the previous session pushed.** Canon only: two register entries, the state file, the
  roadmap. No migration file, by that session's own deliberate choice.
- **This machine.** Every session transcript, every paste cache entry, the dispatcher's job
  directories. The only hits are this paste and my own writing about the design.
- **The session that holds it.** It is listed as reachable, but a cloud session cannot reply into this
  one, and its transcript is not on this box.

## 3. Why I did not reconstruct it

The prose is detailed enough that I could write plausible SQL from it. **That is exactly the problem.**
Composing a column definition, a foreign key, a partial unique index, a check constraint and a rewrite
of a live routine, and then calling the result "the approved SQL", is authoring — and the paste's own
rule is that any further semantic change stops the chunk. A reconstruction that merely *looks* like
the approved text is worse than no reconstruction, because it would pass review as though a gate had
been crossed when none had.

This is the same discipline that made the previous attempt stop, and the same one that made T2 safe:
apply what was approved, verbatim, or do not apply.

## 4. The lesson, and it is a cheap one to fix

**T2 was recoverable and T1 was not, for one reason: the previous report inlined T2's two statements
and only summarised T1's larger block.** Both tasks were blocked with equal care and equal analysis;
only one came back with its text.

**A blocked task's report must carry the SQL verbatim, not a description of it** — the report is the
only artefact that survives into the next session, and a summary cannot re-cross a gate. The
temptation runs the other way, because the larger and more complex the block, the more it feels like
prose is the useful form. It is the opposite: the larger the block, the less likely anyone can
reproduce it from prose, and the more the next session needs the bytes.

## 5. What unblocks this

**One of two things, whichever is easier for the strategist:**

1. **Re-paste the original chunk-2 T1 SQL** — parts (a) and (c) verbatim, plus the identifier rule.
   Part (b) is already in hand and is correct. Nothing else about T1 needs re-deriving: the premise
   was verified, the naming questions were raised, and the required sweeps and tests are enumerated in
   the previous report.
2. **Re-approve T1 from the design in `D218`**, treating it as a fresh GATE 1 rather than a resume —
   which is more work but avoids depending on a paste that may no longer exist anywhere.

**Recommend (1) if the paste still exists, (2) if it does not.** Either way the corrected guard from
this paste should be carried forward unchanged; the previous attempt's analysis of it was right, and
it is the part that was hardest to get correct.
