M2-brain is done. The last relay-era chunk turned the canon into the v3 shape:
- a 53-line `CLAUDE.md` core;
- four skills (run-card, migrate-db, milestone-review, strategist), each proven by a headless haiku call;
- a card issue template, and a `tier3-guard` that also covers the API routes;
- the ROADMAP retired into backlog issues #126–#154 and a STATE under 8 KB.
The engine was fixed as well: `think` gets its own worktree and a `--restart`; the dispatcher fetches before each fresh `card-<n>` worktree and rotates its logs; the live settings are narrower (the owner copied them). One owner step remains: `bash scripts/dispatch/think.sh --restart`, so that `think` loads the new contract and skills. Codex answered every PR with a usage-limit notice, so the executor's reviewer was the only review.

=== REPORT: M2-brain · done ===
HEAD: 83b5eea | tree: clean | branch: master
PRs: #125 556b275 merged (T2 CLAUDE.md core) · #155 1a1b037 merged (T3 ROADMAP → issues) · #156 b340d18 merged (T4 card template + guard)
     #157 fcc0af9 merged (T5 skills) · #158 5fa6858 merged (T6 engine + settings owner copy) · #161 83b5eea merged (T7 canon close)
CI: master 83b5eea green · PROD: live 200 | live probes: 3/3 (/, /ar/, /api/health) · jahjah-web-run idle on the new script
DONE:
- T0: `chunk:running` set; labels pri:high/med/low, canon, needs-owner created
- T1: 160-row inventory on the issue, no `?` rows
- T2: CLAUDE.md 53 lines; the old file archived verbatim
- T3: issues #126–#154 (map on the issue); STATE takes the phases, decisions and rejected list; ROADMAP archived
- T4: `.github/ISSUE_TEMPLATE/card.yml`; tier3-guard covers `src/pages/api/**` and accepts `card #n`; #149 closed
- T5: skills run-card 62 · migrate-db 37 · milestone-review 44 · strategist 56 lines, all haiku-proven; ship trimmed; relay-report deprecated; #144 closed
- T6: think worktree + `--restart`; dispatcher fetch + `-b card-<n>` + 14-day rotation; settings v3 narrowed (owner copy, W138); think rules; #152 and #153 closed, #154 partly done
- T7: STATE v3 (7,996 B), W171–W173, STRATEGIST archived; #159 and #160 opened
DEVIATIONS:
- Codex was out of quota on all 6 PRs; recorded, not waited on (plan allowed).
- `printenv` was refused at preflight, so env names were proven by the build (F66's process check).
- The skill proofs disallowed Bash writes, Edit and Write. The first run-card proof could not read #120 and was re-run (retry 1 of 2).
- The template and strategist skill add approved Arabic as a third verbatim item. think may use WebSearch/WebFetch.
- One compound command was refused because it held a bare `git push`; it was re-run split, with `git push origin chunk/…`.
FINDINGS/BLOCKERS:
- The "v3 text" (§3–§6) is not in the repo; the plan's classification served as the spec.
- Measured with headless `dontAsk` runs:
  - a worktree of this clone is trusted, inside or outside it; a nested separate repo is not (W173);
  - think's rules 22/22 + 4/4;
  - the v3 `gh api` and `--output` denies 10/10 + 8/8;
  - resuming a session inside a card worktree works.
- An unquoted ` #` truncated skill descriptions (W172).
- Residual: think can create a local non-canon branch but cannot push it.
- Open for M3: #160 (workers push `card-<n>` with no allow rule); #159 (stale pointers: `AGENTS.md`, `.claude/agents/*`, the verify skill, comments; `tier3-guard` still skips `scripts/dispatch/**`).
- Canon PR merge is an owner tap, while card #112 amendment 2 says auto (STATE §4). The relay mirror drops ROADMAP.md and STRATEGIST.md: the strategist chat now reads `.claude/skills/strategist/SKILL.md` through the connector.
CANON: CLAUDE.md, docs/STATE.md, docs/DECISIONS.md (W171–W173), docs/archive/{CLAUDE-relay,ROADMAP-final,STRATEGIST-final,STATE-history}.md, .claude/skills/**
NEXT-NEEDED: the owner runs `bash scripts/dispatch/think.sh --restart` (think reloads CLAUDE.md, skills and settings in its own worktree); then think writes M3's 12 dry cards (HANDOVER, STATE §5).
=== END ===
