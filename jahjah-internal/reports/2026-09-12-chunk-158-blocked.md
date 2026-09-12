KIND: blocked

The five canon essays are restructured into RULE / MEASURED FACT / TEST PIN bullets, the
meta-narrative is gone, the work engine's address is out of `docs/`, and `scripts/canon-lint.sh`
now caps all five files — PR #161, CI green. What this chunk did NOT do is hit the size target: the
files came down to 90 % of their starting bytes together, not the 40 % the plan asked for. The
acceptance lets me name a file that *cannot* be cut that far; I measured whether that was true and
it is not, so I am not claiming it. The identifiers I had to preserve are only 9–18 % of each file
— the rest is my own prose, which I restructured but barely shortened. A second, telegraphic pass
would get there. I have left #161 open rather than merging, because the size cut was the point of
the chunk and whether to land the restructure without it is your call, not mine.

DECISION NEEDED — A or B:
  A. Merge #161 as it stands and open a follow-up chunk for the size cut. The restructure, the
     de-duplication, the address removal and the caps are independently worth having, and the caps
     are set at landed+10 % so a later pass can only lower them. RECOMMENDED.
  B. Hold #161 and re-run the size cut on this branch first, as one more chunk against the same
     files.

=== RELAY ===
HEAD: a4cb0bc | tree: clean
CI: pass — ci-ok green, all 11 checks, run 34665184326 (PR #161); no post-merge run, nothing was merged
DONE: five essays restructured to RULE/FACT/PIN; two reviewer passes caught eleven measured facts and cross-references my token-only proof could not see, all restored and re-reviewed clean; meta-narrative and duplicated residual lists removed; work engine IPv4 replaced by "the work engine" in docs/; five byte caps added to scripts/canon-lint.sh and proven by an oversize scratch run; identifier-preservation proof per file in the PR body
FILES: 6 — docs/pitfalls/{inventory-stock,money-fx,infra-vps}.md, docs/runbooks/{backup,automations}.md, scripts/canon-lint.sh
FINDINGS/BLOCKERS: size target missed — 82.1/77.0/96.8/94.0/94.8 % against a 40 % target, and it is reachable, not blocked; the address had NO functional occurrence in code or config, so there is nothing to list there; docs/runbooks/automations.md is the one of the five on the public mirror and one flag name I had added to it was moved back off (D235)
NEXT-NEEDED: A or B above
=== END ===
LESSON: Restructuring prose into a labelled shape does not shrink it — measure the irreducible payload first, then write to the byte budget that leaves.
