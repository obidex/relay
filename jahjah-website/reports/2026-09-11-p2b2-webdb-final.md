P2b-2 is done. The website has its own database (Supabase project #2), with the approved schema applied exactly as written. Row-level security keeps visitors out, every write is audited, and staff writes require TOTP. The site's server code has typed readers for it, and nothing public changed. Two known gaps in the approved SQL expose nothing today because the tables are empty: helper functions visitors can call, and stock quantities customers could read. They are recorded as F65, to be fixed by P2b-3's first migration under GATE 1. The next step is P2b-3, the first on-demand route, which waits on Vercel Pro.

=== REPORT: P2b-2-webdb · done ===
HEAD: 41de536 | tree: clean | branch: master
PRs: #86 3047ac8 merged · #87 0173d8b merged · #88 41de536 merged (canon close)
CI: master run on 41de536 success · PROD: Production deployment success | live probes: 2/2 (`/` 200, `/ar/` 200)
DONE:
- T0: labels moved. One BLOCKED stop (the platform object in `db diff`), ruled A; resumed with labels re-moved.
- T1 (#86): link, the migration byte-identical (GATE 1, no identifier fixes), `db push`, `migration list` 1/1, `db diff` empty beyond `rls_auto_enable`/`ensure_rls`, smoke exact, anon 0 rows on 7 tables.
- T2 (#87): `src/lib/{settings,prices,stock,customers}.ts`, `types.ts` aligned, `env.ts` `supabaseProjectRef()`. `tsc` over `src/lib/**` exit 0 (negative test exit 1). Build 68; verify 0/0/67; "supabase" in `dist/` + `.vercel/output` = 0.
- T3 (#88): STATE, ROADMAP (P2b-2 done, P2b-3 defined, F64–F66), DECISIONS W153–W157; reference with no drift.
- Review: executor reviewer on each PR (T1: 1 FIX + 1 NOTE, fixed; T2 CLEAN; T3: 1 FIX, fixed). Codex: #86 one P1, answered and routed to F65; #87 👍; #88 👍.
DEVIATIONS: none. The only SQL run was the approved migration. Scratch-only helpers (`.astro/`, never committed) ran the CLI with two env names per command and redacted output.
FINDINGS:
- F65 (high, before any stock row or auth route): anon EXECUTE on the helpers via `PUBLIC` (accepted residual per the ruling), and customers' `stock` reads return `quantity` (Codex P1). Both are fixed in P2b-3's first migration under GATE 1.
- F64: the web-DB `pg_dump` is ERP-side. Until it exists, free Supabase pauses after ~1 week idle (W091).
- F66: `grep -c '^SUPABASE_' .env.local` was refused on resume. I did not route around it; the names were proven by the processes that read them.
- Supabase CLI 2.117.0 installed globally with `npm i -g supabase`, as the plan allowed.
CANON: docs/STATE.md, docs/ROADMAP.md, docs/DECISIONS.md (W153–W157); docs/reference/site.md (T1, T2)
NEXT-NEEDED: owner at a PC for P2b-3: Vercel Pro, the three server-only Supabase names in Vercel, and `SANITY_WRITE_TOKEN` if not yet created
=== END ===
