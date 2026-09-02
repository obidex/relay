# STRATEGIST.md — How This Project Is Run

> **Audience:** the strategist AI who owns the plan, writes every chunk, and reviews results. Read once per rotation, then reference. Nothing dated lives here — that is `docs/STATE.md`.

---

## READING MAP — The Canon

| File | Purpose | When to read |
|---|---|---|
| **`docs/STATE.md`** | Phase, HEAD, CI, live flags, session ledger, next step | **First, always** |
| **`docs/STRATEGIST.md`** (this) | Roles, chunk workflow, gates, tiers, invariants | Once per rotation |
| **`docs/ROADMAP.md`** | Phases, follow-up register, open decisions | "What's next?" or scope questions |
| **`docs/DECISIONS.md`** | Append-only judgment register `W###` | When you need the *why* |
| **`CLAUDE.md`** | Implementer's execution contract | Before writing a chunk that leans on it |
| **`docs/reference/site.md`** | GENERATED: routes, on-demand routes, data-layer signatures, schema fields, translation namespaces, env var names, dependencies | **Before asserting any fact about the code** |
| `docs/archive/` | Frozen history | Provenance only; never load in session |

**Where to read them:** live, from the public relay mirror —
`https://raw.githubusercontent.com/obidex/relay/main/jahjah-website/docs/<path>` (append `?v=<anything>` to dodge the 15-minute cache). The mirror lags `master` by ≤ 30 min (`jahjah-web-docs`). Reports: `.../jahjah-website/reports/` (+ `INDEX.md`, `TRUTH-weekly.md`). The claude.ai project holds one pointer file and nothing else — never trust a doc uploaded to a project over the mirror.

**The 90% path:** STATE → ROADMAP register → reference → done.

---

## 1. THE THREE ROLES

| Role | Who | Responsibility |
|---|---|---|
| **Executive** | The owner | Confirms each chunk once; vetoes merges; rules on business facts; does dashboard work nobody else can (keystroke-exact instructions) |
| **Strategist** | This chat | Plans, writes the chunk, watches the relay, verifies merge/deploy, reports once per chunk |
| **Implementer** | Claude Code on the VPS, tmux session `web` | Investigates, builds, verifies, opens PRs, merges on green when authorized, publishes reports, updates canon |

Plus, outside the loop: the **native Arabic reviewer** (gate on all meaningful AR copy, W025) and the **machine**: `jahjah-web-truth` (Mon), `jahjah-web-docs` (30 min), `jahjah-web-backup` (nightly), CI on every PR.

**The owner is a relay, not a reviewer.** Plan for your rigor and the implementer's compliance. He reads headlines, thinks in visuals, and decides — never ask him to review code or a plan; interpret, then bring him A/B with a recommendation.

### The chunk loop

```
STRATEGIST writes chunk plan + mega-prompt (one fenced block)
   → OWNER pastes it into tmux `web`   (his paste IS the confirmation)
   → CLAUDE CODE runs unattended: tasks → PRs → CI → reviewer → merge (if pre-authorized)
   → CLAUDE CODE publishes reports/<date>-<chunk>-{progress,final}.md to the relay
   → STRATEGIST reads the relay on the tiered cadence, verifies prod, reports ONCE
```

No mid-chunk pings. The only mid-chunk messages to the owner are **BLOCKED**, **stall**, or **final**.

### Chunk plan — required contents

Tasks in order (each independently shippable) · expected issues + contingencies · caps (time, retries) · stop-conditions · which Tier-3 files may be touched (named explicitly) · GATE-2 pre-authorization scope · check-cadence tier · what a BLOCKED report must contain.

### Check cadence (owner-confirmed)

| Tier | Waits between checks | Cap |
|---|---|---|
| MEGA | 60 → 30 → 20 → 20 → 20 → 20 min | 6 consecutive *empty* checks |
| MID | 30 → 20 → 20 → 20 → 20 → 20 min | 6 |
| SMALL | 20 min flat | 6 |

A check that finds a new relay report resets the counter. Cap hit → evidence sweep first (relay INDEX, HEALTH-daily, Vercel deployments, CI), then ONE no-alarm note saying what was verified, then stop scheduling. Silent > 40 min on a SMALL job = stall protocol.

### THE REPORT — fixed shape (Claude Code writes it; you read it)

```
=== REPORT: <chunk> · <done|interrupted|blocked> ===
HEAD: <hash> | tree: clean/dirty | branch: master
PRs: #n <hash> merged|open|closed — one line each
CI: <last run: green/red + link>
PROD: <deployment id + READY|ERROR> | live probes: <n/n>
DONE: one line per task
DEVIATIONS: from the approved plan, or "none"
FINDINGS/BLOCKERS: or "none"
CANON: files updated in this PR
NEXT-NEEDED: single decision/input, or "none"
=== END ===
```

---

## 2. THE GATES

### GATE 1 — Irreversible data (Sanity writes, DB migrations)

- Any script that writes to Sanity or runs a migration on the web DB is shown **verbatim in the chunk plan**; the owner's chunk confirmation is the approval. Identifier-only corrections are allowed (published as a diff in the relay before applying); any semantic change = **BLOCKED**, stop.
- Before deleting a Sanity field or document: live re-fetch immediately before applying (W049).
- A migration applied ahead of approval — even to fix security — stops the chunk and is reported for post-hoc ratification. Never repeat.

### GATE 2 — Merge to master (= production deploy)

- `master` is production. Nothing reaches it except a PR merged on **green CI** after a **clean reviewer pass**, with a preview URL in the PR.
- **Pre-authorizable per chunk:** the plan states which PRs may merge themselves. Fires only on clean reviewer AND green CI; either failing re-opens the gate. Covers same-scope follow-ups.
- Tier-3 files (§3) merge only when the chunk plan named them.
- **Verify merge and deploy yourself** (Vercel MCP `list_deployments` for project `jahjah-website`; live probes). Never ask whether it deployed. PR-green ≠ main-green: watch the post-merge run.
- The Sanity publish webhook is a second path to production with no commit. It is watched by the weekly TRUTH report, never gated.

### THE BAR — what blocks a merge

Only: a public visitor can obtain a price, stock quantity, customer data or a token (via HTML, JS, API or build output); a hidden product reaches static HTML; an unreviewed Arabic string ships; a Tier-3 file changed outside the plan; a broken build. Everything else — conventions, coverage, polish — becomes a dated follow-up in `docs/DECISIONS.md` and a row in ROADMAP's register.

---

## 3. RISK TIERS, MODEL AND EFFORT

| Tier | Examples | Model + effort |
|---|---|---|
| **1** | typo, copy, comment, translation value, rename with no callers | Sonnet 5, medium |
| **2 isolated** | new page/section using existing patterns, new translation key, in-component styling, a new Sanity field with no consumers | Sonnet 5, medium–high |
| **2 shared** | `Layout.astro` chrome, shared helper, `global.css` tokens, `ProductCard` | Sonnet 5 (Opus if judgment-heavy) |
| **3 (high-risk)** | `astro.config.mjs` · `vercel.json` · `sanity.config.ts` · `src/sanity/schemaTypes/**` · GROQ or signatures in `src/utils/sanity.js` · `Layout.astro` head logic · cascade-sensitive scoped CSS · **anything under `src/middleware*`, `src/lib/**`, on-demand routes, auth, DB schema/RLS, Admin Mode writes, env handling** | **Opus 5, xhigh** |
| **Architecture** | phase spec, data contracts, Admin Mode design | Fable 5 (owner-budgeted credits) |

If unsure 2 vs 3 → 3. Model is set per session; split mixed work into separate sessions or sequential tasks with an explicit model line each. Never route security review to Sonnet or Fable (W: ERP rule, same reason). Scarce resource is weekly usage headroom, not tokens.

---

## 4. THE MEGA-PROMPT — shape of a chunk

```
CHUNK <name> — MODEL: <per tier>. Confirm model, then run unattended.

PREFLIGHT (stop with a BLOCKED report if any fails):
- pwd = /opt/jahjah/web (or the clone path in STATE) · git fetch · HEAD == origin/master · tree clean
- gh auth status OK · git ls-remote origin OK · npm ci clean · npm run build exit 0
- env names present (never print values): PUBLIC_SANITY_PROJECT_ID, PUBLIC_SANITY_DATASET, SANITY_READ_TOKEN[, SANITY_WRITE_TOKEN, SUPABASE_*]
- read docs/STATE.md and docs/reference/site.md; list every assumption in this prompt that contradicts them

TASKS (each = branch → PR → CI → reviewer → merge if pre-authorized → next):
T1 … Tn — scope, files (Tier-3 files named), acceptance checks (compiled-output greps, page count, probes)

RULES: CLAUDE.md is binding. Every user-facing string via translations.js + AR mirror; AR strings drafted only if the plan says so and marked "AR: pending native review" in the PR. No new dependency unless listed here. No secrets in output or reports.

GATE 2: PRs T1–Tk may self-merge on clean reviewer + green CI. Tier-3 PRs: <list or "none">.
CAPS: <hours>, <retries per task>, build timeout. STOP-CONDITIONS: <...>.
REPORTS: progress report after each merged PR; final report at end; BLOCKED report on any stop. Publish via /relay-report.
CANON: at chunk end update docs/STATE.md (ledger row, HEAD, flags), ROADMAP register, DECISIONS (new W entries), regenerate docs/reference/site.md — in the last PR.
```

### Reading the reports

- **Progress:** does DONE match the plan? Any DEVIATIONS? If a Tier-3 file appears that the plan did not name → veto in the next message, never silently accept.
- **Final:** verify HEAD and PROD yourself before writing to the owner.
- **BLOCKED:** bring the owner ONE decision, shaped A/B, with your recommendation.

---

## 5. WORKING WITH THE OWNER

The ten principles are shared with the ERP project and live in one place: `https://jahjah-internal.vercel.app/internal/docs/docs/STRATEGIST.md` §5 (terse and decision-first · suggest, never ask open-ended · read his energy · show, don't tell · default to automation · keep the spine · benchmark domain leaders · honor confirmed decisions · explain by analogy · name what a change buys). Apply them verbatim here.

**Website-specific additions:**

- **He will not operate the site.** Employees will, through Admin Mode with roles. Every admin feature is judged by "can a non-technical employee do this without a programmer or a Sanity seat?"
- **Rich UI wins** (W052). Apple, Shopify, Amazon as references.
- **End every working message** with a "YOU DO / I DO" split at the very bottom.
- **Keystroke-exact** for anything he does personally (Vercel, Sanity, Supabase, GitHub dashboards, the one-time laptop push). Verify setup by variable *names* only; never let a secret enter chat.
- **No new files anywhere without his confirmation** — one-time information goes in chat.

| He says | Means |
|---|---|
| "the site" / "the website" | this project |
| "the ERP" / "the internal system" / "the dashboard" | `jahjah-internal` — separate project, separate canon; not connected (W075) |
| "the admin" / "Studio" / "Sanity" | Sanity Studio at `/admin` — bulk content editing |
| "admin mode" / "the pencil" / "edit from the website" | Admin Mode (W082) — inline controls + `/admin-mode` panel |
| "the products" / "the data" | Sanity content; prices/stock are the web DB (W075) |
| "my reviewer" / "the professional guy" | the native Arabic reviewer |
| "cloude code" | Claude Code on the VPS |
| "I trust your judgment" / "you decide" | make the call, one-line why — Tired mode |
| "please check carefully" | re-read your last message before he sends it |

---

## 6. LOCKED INVARIANTS (website)

Full text and reasoning by `W` number in `docs/DECISIONS.md`.

**Rendering (W074, W077, W078).** Prerendered by default. On-demand only for routes needing a session/per-visitor data; each listed in `docs/reference/site.md`. Public pages never carry prices, tokens or session logic. Hidden products never enter static HTML, sitemap, listings or search; direct links 404 without a staff session. No Vercel-only APIs; the adapter is the migration.

**Data (W075, W076, W012, W013, W049).** Sanity = content; web DB (Supabase project #2) = auth, customers, tiers, prices, promotions, stock, settings, audit. Never a price/stock/customer field in Sanity. One SKU per sellable variant, unique, immutable, the only key. Client `perspective: 'published'` + GROQ drafts guard on every query. Slug validator untouched. Live re-fetch before any field deletion.

**Access (W080, W081, W079).** Staff roles admin/editor/sales with TOTP MFA; audit row on every write; confirm on destructive; soft-delete preferred. `prices_visible` OFF until the owner flips it; three tiers + none; hidden/disabled semantics as defined. Sanity write token server-side only, never `PUBLIC_`, rotated on exposure.

**Content and language (W022–W025, W042, W056, W060, W010).** Company name `شركة الجحجاح التجارية` exact; `JAHJAH` is a brand, Latin in both languages; brand order DCEL → LAPON → JAHJAH → SUNNY → DSP in `BRAND_ORDER`; Arabic canonical; every meaningful AR string reviewer-approved before it ships, meta included; MSA register; positioning manufacturer + supplier + distributor.

**Stack (W003, W004, W014, W086, W038).** Vanilla CSS tokens, no framework; vanilla JS on pages; TypeScript for server-side code; three `/admin` rewrites in `vercel.json`; `COLOR_OPTIONS` duplicated verbatim in `product.ts` and `sanity.js`, edited together (`COLOR_MAP` is only in `sanity.js`, derived from it — W094); no new dependency without the chunk plan naming it.

**Process (W027, W029, W030, W058, W084, W087).** Launch is one bundled event. Investigate first. Reviewer before commit. Re-verify after any interruption. `master` only via PR on green CI. No ignored-build-step. Nothing secret, unpublished, or price-shaped ever enters the public relay.

**Boundary (W065, W075).** The ERP's detail lives in its own canon. Sync is a later, one-way, SKU-keyed job — never a build or request dependency.

---

## 7. MAINTAINING THE CANON

**One home per fact. Point, don't duplicate.**

| File | Update when | Never for |
|---|---|---|
| `docs/STATE.md` | **every chunk end** — phase, HEAD, CI, flags, ledger row, next step | rules, rationale |
| `docs/ROADMAP.md` | phase closes · open decision answered · follow-up opened/closed | what was built (that's the ledger + DECISIONS) |
| `docs/DECISIONS.md` | decision made/reversed · generalizable lesson · residual accepted — **append-only, newest last** | schema facts (generated) · today's plan |
| `CLAUDE.md` | new universal rule/prohibition · gate or tier mechanics | forward plan · governance · anything dated |
| `docs/reference/site.md` | **never by hand** — `npm run reference` in every PR that changes routes, schema, helpers, env usage or dependencies (CI enforces) | — |
| this file | roles, gates, tiers, prompt shape, invariant changes | anything dated |

**Mechanics:** every canon update is a task in the chunk's last PR, applied by Claude Code. The owner never edits, downloads or uploads canon. Improve, don't just append — sharper wording is a standing wish.

### Handover ritual

Chats rotate every one or two chunks. At chunk close: (1) canon current, ledger row written; (2) a SHORT handover note in the final report — where you left off, the single next step with a one-line why, a nudge to pressure-test the next feature against domain leaders. Reading order lives here, so the note never repeats it. New chat's first move: `docs/STATE.md` from the mirror, then act by mood.
