# P0 · Canon reset — BLOCKED at preflight

<!-- index: P0 canon reset stopped at preflight — the .claude/ agentic layer never left the laptop (the repo's own .gitignore ate it); nothing landed, master untouched -->

**Generated (UTC):** 2026-09-02T02:45:13Z · **Executor:** VPS `germany-vpn`, tmux `web`, Claude Code (Opus 5, xhigh) · **Chunk:** P0 canon reset

## For the owner — one paragraph

The canon you pushed is **17 files short**, and none of them are your fault in the way it looks: the
repo's own `.gitignore` has a line that says "ignore `.claude/`", so when you added the files on the
laptop, git silently skipped the entire `.claude/` folder — the reviewer, the deny rules, and the
`/verify` `/ship` `/relay-report` shortcuts. Everything else arrived intact. I stopped before changing
anything: nothing was merged, `master` is exactly where it was, and the live site is untouched and up.
**I need one thing from you: re-push that folder from the laptop with the two-line `.gitignore` change
below** (keystroke-exact, further down). It is about a two-minute job. The moment it lands, this chunk
restarts from the top and runs to the end unattended. While I was there I also found three real
problems in the new canon itself — one of them is a safety check that currently passes by accident —
all written up below for the strategist, all fixable inside the resumed run.

-----

```
=== REPORT: P0 canon reset · blocked ===
HEAD: master 9bdcb40 (unchanged) | tree: clean | branch: canon-reset b3357f4 checked out, unmodified
PRs: none opened
CI: never ran — no PR was created; ci.yml exists only on the unmerged branch
PROD: untouched (no deploy triggered) | live probes: 2/2 — / 200, /ar/ 200
DONE: preflight 1-4 PASS; preflight 5 FAIL -> stop. T0-T4 not started.
DEVIATIONS: after the stop I ran read-only diagnostics (verify.sh, the reference generator, live
  probes) that the plan schedules for T1c/T1d, to make this report one round trip instead of two.
  Nothing was committed, pushed, or set; the working tree was restored to origin/canon-reset exactly.
  No GitHub secret was set (T0 not run: preflight gates the chunk).
FINDINGS/BLOCKERS: B1 (blocker) + F1, F2, F3 (findings) — below
CANON: none updated — nothing was committed in either repo
NEXT-NEEDED: owner pushes .claude/ to branch canon-reset with the .gitignore fix (B1). Second, smaller
  input for the strategist at resume: rule on F1 (CI is red on day one otherwise) — recommendation given.
=== END ===
```

-----

## B1 — BLOCKER: the entire `.claude/` layer is absent from the branch

`git diff --stat master...canon-reset` lists **9 files, not 26**:

| Landed on `canon-reset` | |
|---|---|
| `CLAUDE.md` · `docs/STRATEGIST.md` · `docs/STATE.md` · `docs/ROADMAP.md` · `docs/DECISIONS.md` | 5 |
| `docs/archive/HISTORY.md` | 1 |
| `scripts/verify.sh` · `scripts/generate-reference.mjs` | 2 |
| `.github/workflows/ci.yml` | 1 |
| **Total present** | **9** |
| **Absent — every one of them under `.claude/`** | **17** |

`.github/` is fine. `.claude/` is absent from `canon-reset` **and** from `master` — `git ls-tree -r canon-reset -- .claude` returns nothing — and no copy of it exists anywhere on this VPS. The content
exists only on your laptop.

**Cause (this is the part that matters).** The repo's own `.gitignore`, on both branches, carries:

```
27  # Claude Code local workspace state
28  .claude/
29  src/.claude/
```

So `.claude/` was never dropped by an extract — it was **ignored by the repository itself**. A bulk
`git add -A` / `git add .` skips ignored paths without printing a word; an explicit `git add .claude`
would have refused with a message that scrolls past easily. That rule was written when `.claude/` held
only local workspace state. As of this canon it holds **canon** — which is why the rule has to change
at the same time as the files land, or the next `git add` will eat them again.

**What the canon says should be in there** (from the files that did land):

| Path | Named in | What it is |
|---|---|---|
| `.claude/settings.json` | `docs/DECISIONS.md` W084, `docs/STATE.md` §3 | the machine gate: deny push to `master`, deny force-push, deny `.env*`, deny destructive Sanity CLI verbs |
| `.claude/agents/reviewer.md` | `CLAUDE.md` §5, §4 (Tier 2/3 ceremony) | the reviewer subagent — a clean pass is half of GATE 2 |
| `.claude/skills/` — `/verify`, `/ship`, `/relay-report` | `docs/STATE.md` §3, `CLAUDE.md` §5 | the three procedures the chunk plan invokes by name |

**Why I could not proceed without them.** T1f requires a clean pass from `.claude/agents/reviewer.md`;
GATE 2 in this plan fires only "on green CI + clean reviewer", so no PR in this chunk may merge without
it. T2c is *defined* as confirming Claude Code loads `.claude/settings.json` from the clone. And landing
the canon as-is would publish `DECISIONS.md` W084 and `STATE.md` §3 asserting that a machine-enforced
push gate exists, while the mechanism is not in the repo — the canon would be lying about itself on its
first commit. `.claude/**` is Tier 3 in the new `CLAUDE.md` §4 and this plan authorised *landing* those
files, not *authoring* them; I will not write the deny rules or the reviewer from scratch and pass them
off as the layer you approved.

### THE FIX — for the owner, on the laptop

```
cd <your jahjah-website folder>
git checkout canon-reset
git pull
```

Open `.gitignore`. **Replace line 28** — the single line `.claude/` — with these five lines
(leave line 29, `src/.claude/`, exactly as it is):

```
.claude/*
!.claude/agents/
!.claude/skills/
!.claude/settings.json
.claude/settings.local.json
```

Then:

```
git add .gitignore .claude
git status
```

`git status` **must now list** `.claude/settings.json`, `.claude/agents/reviewer.md` and your
`.claude/skills/...` files. If it still lists nothing, the folder is empty on that machine — that is a
different problem, tell the strategist. If it lists them:

```
git commit -m "chore: track the .claude agentic layer (canon, not local workspace state)"
git push origin canon-reset
```

That is all. Do not merge anything; I do that. *(Why the five lines rather than `git add -f .claude`:
force-add lands today's files but leaves the folder ignored, so a skill you add next month goes missing
the same silent way — and CI's reference-drift check reads the folder off disk, so the laptop and CI
would disagree without either one erroring.)*

-----

## Findings from the diagnostic pass

### F1 — `verify.sh` fails on `master` TODAY: 7 broken internal links. CI would be red from the first run.

The plan expected "0 FAIL, WARNs for placeholder.jpg and /admin-in-sitemap". Reality, run against a
clean `master` build on this box:

```
2 FAIL · 3 WARN · 67 pages
FAIL  page count 67, expected 68            <- F3, separate
FAIL  7 broken internal links/assets
      /ar/404/
      /images/categories/cooling.jpg   /images/categories/kitchen.jpg
      /images/categories/laundry.jpg   /images/categories/refrigeration.jpg
      /images/categories/small-appliances.jpg   /images/categories/tvs.jpg
WARN  asset missing (known until P1): /images/placeholder.jpg
WARN  /admin in sitemap (known until P1)
WARN  2 scope-injected [dir=rtl] selectors (dead rules, W019)
```

**These are true facts about the live site, not check bugs, so I did not touch the site** (and `src/**`
is off-limits in this plan regardless). Confirmed in production:
`GET /images/categories/cooling.jpg` -> **404**, same as `/images/placeholder.jpg` -> 404.

- **The six category icons.** `src/utils/sanity.js:154` falls back to `/images/categories/${slug}.jpg`
  when a category has no Sanity icon. `public/images/categories/` contains only `ac.jpg`, `fan.jpg`,
  `washer.jpg` — none of which is a category slug. So all six categories render a broken icon today.
  Same class as the known `placeholder.jpg`; the WARN exception list simply never learned about them.
- **`/ar/404/`** is linked from `dist/404.html` alone — the Arabic hreflang alternate on the 404 page
  points at a route Astro never emits. A real dead link, SEO-only, P1-class.

**The consequence is process, not pixels:** land the canon unchanged and every PR and every `master`
run is red, so "merge only on green" (W084) is unusable from the day it is written.

**Decision needed (strategist), A/B:**
**A (recommended)** — widen the known-until-P1 exception in `verify.sh` to name these seven paths
*individually* alongside `placeholder.jpg`, all still promoted to FAIL by `STRICT_P1=1`. Green then
means "no NEW breakage", the P1 exit criterion keeps its teeth, and a genuinely new broken link still
fails loudly. One edit to `scripts/verify.sh`, inside this chunk's Tier-3 authorisation.
**B** — leave the check strict and accept red CI until P1 lands the assets. Honest, but it trains
everyone to merge red in the first week, which is the exact habit W084 exists to prevent.
A glob (`/images/**`) is the wrong shape for either: it would hide the next missing asset silently.

### F2 — the `COLOR_OPTIONS` / `COLOR_MAP` sync guard passes by accident (vacuous green)

`docs/reference/site.md` §"COLOR_OPTIONS / COLOR_MAP sync (W038)" generates:

| File | Slugs |
|---|---|
| product.ts | *(empty)* |
| sanity.js | *(empty)* |

**Status: IN SYNC** — because two empty lists compare equal. The extractor is not reading anything.

`scripts/generate-reference.mjs:117-118` matches `value: '...'` and `foo: {`. Both source files
declare the palette as `{ slug: 'white', nameEn: ..., nameAr: ..., hex: ... }` — the key is **`slug:`**,
which neither pattern matches. So W038's automated guard reports a clean bill of health on an
extraction of zero items, and would keep saying IN SYNC if the two palettes diverged completely.

Fix is one line in the extractor (never the output contract, never the source), for the resumed T1c:

```js
for (const m of block.matchAll(/slug:\s*['"]([A-Za-z]+)['"]/g)) set.add(m[1]);
```

Worth noting for the reference's wording: `sanity.js` derives `COLOR_MAP` from its own `COLOR_OPTIONS`
via `Object.fromEntries`, so the invariant that actually needs guarding is between the **two
`COLOR_OPTIONS` literals** in `product.ts` and `sanity.js`.

### F3 — `EXPECTED_PAGES=68` is set against a different metric than the check that consumes it

Not a bug in either — a units mismatch:

| Counter | Counts | Value |
|---|---|---|
| Astro build log | every emitted page, `/admin` included | **68** |
| `verify.sh` §1 | non-`admin` `index.html` (66) + root `*.html` other than `index.html` (`404.html`, 1) | **67** |

`ci.yml` and the plan both carry 68; `scripts/verify.sh` deliberately excludes the Studio SPA. The real
count for the consumer is **67** — set `EXPECTED_PAGES: "67"` in `.github/workflows/ci.yml` at the
resumed T1d, and expect the plan's "68" to reappear in future chunk prompts unless STATE §1 records
which number means what. (STATE §1 currently says "68 pages" for the live site, which is Astro's number
and correct for that sentence.)

### Minor, no action

- The reference generator is **idempotent** — two consecutive runs are byte-identical, so CI's
  `git diff --exit-code -- docs/reference/` will not flap. Routes (18 route files incl. `/ar/*`,
  `brands/[slug]`, `products/[slug]`, `how-to-buy`, `404`, `admin/[...all]`), `sanity.js` exports,
  schema groups, env **names**, and dependencies all extract correctly. Translation namespaces come out
  **SYMMETRIC**, 12 namespaces, EN keys == AR keys in every one.
- Its "Repo automation surface" section walks `.claude/` and therefore currently emits **no rows for
  it at all** — it will read as complete rather than as missing. It self-corrects once B1 lands.
- `/opt/jahjah/session/state` exists on the box, empty, with no unit and no registry entry. Harmless,
  but it is the kind of unexplained thing the automations runbook §1 says to stop on. I used
  `JJ_JOB=session` with the shared library to publish this report under the relay flock, since
  `/relay-report` is one of the missing skills — that writes only `/opt/jahjah/session.log` and that
  state dir, and touches no other job.

-----

## What passed, for the record

| Preflight step | Result |
|---|---|
| 1 · env file | `$WEBENV` = the `jahjah-web-truth` job's own read-only env file. `grep -cE` of the three names -> **3**. Values never read, printed or copied anywhere but step 3. |
| 2 · clone + push rights | `gh repo clone` -> `/opt/jahjah/web`. `gh auth status` OK (account `obidex`; scopes `gist`, `read:org`, `repo`, `workflow`). `git ls-remote --heads origin` lists `canon-reset` at `b3357f4`. `git push --dry-run` clean; API confirms `push: true`, `admin: true`. |
| 3 · `.env.local` | Created from the three lines only, `chmod 600`, never printed. `git check-ignore -v` -> ignored at `.gitignore:18`. |
| 4 · toolchain + build | `node v22.23.2` · `npm ci` clean (1158 packages, 42s) · `npm run build` **exit 0, 68 pages, 32s** |
| 5 · branch contents | **FAIL — B1.** 9 of 26 files present; `.claude/` absent. |

**Not done, deliberately:** no GitHub Actions secret was set (T0 sits behind preflight), no commit, no
push, no PR, no merge, no unit installed, no change in `obidex/jahjah-internal`, no Vercel or Sanity
interaction. `/root/jahjah-website` — the working copy `jahjah-web-truth` reads — was not touched.

**One thing for the strategist to decide separately at resume:** `jahjah-web-truth` reports git facts
about `/root/jahjah-website`, but the executor clone is now `/opt/jahjah/web`. From the moment this
chunk lands, TRUTH's "ahead/behind, clean/dirty" section describes a clone nobody works in. Either
point that job at `/opt/jahjah/web` (a one-line change in its script, T3c-adjacent) or accept that the
section is about a spare copy — but do not leave it undecided, it reads as authoritative.
