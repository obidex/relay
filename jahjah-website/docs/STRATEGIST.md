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
`https://raw.githubusercontent.com/obidex/relay/main/jahjah-website/docs/<path>` (append `?v=<anything>` to dodge the 15-minute cache). The mirror lags `master` by ≤ 30 min (`jahjah-web-docs`). Reports: `.../jahjah-website/reports/` (+ `INDEX.md`, `TRUTH-weekly.md`). The claude.ai project knowledge is a **GitHub sync** of `docs/` + `CLAUDE.md` (W098) — useful for orientation and for finding which file owns a fact, but it lags; **the mirror wins**, and never upload a doc to a project. **A fresh `INDEX.md` does not mean a fresh sibling (W102):** `raw.githubusercontent.com` can serve a current index and a stale body in the same second, cache-buster on both, so trust INDEX's "Mirrored commit" line over the body you just fetched. Code, PRs, CI and issues are read through the **GitHub connector** (§8), which is fresher than the mirror.

**The 90% path:** STATE → ROADMAP register → reference → done.

---

## 1. THE THREE ROLES

| Role | Who | Responsibility |
|---|---|---|
| **Executive** | The owner | Confirms each chunk once; vetoes merges; rules on business facts; does dashboard work nobody else can (keystroke-exact instructions) |
| **Strategist** | This chat | Plans, writes the chunk, watches the relay, verifies merge/deploy, reports once per chunk |
| **Implementer** | Claude Code on the VPS, tmux session `web` | Investigates, builds, verifies, opens PRs, merges on green when authorized, publishes reports, updates canon |

Plus, outside the loop: the **native Arabic reviewer** (a **batched** pass over all meaningful AR copy — never a merge gate, W125 superseding W025) and the **machine**: `jahjah-web-dispatch` (every 2 min — the one that now starts a chunk), `jahjah-web-docs` (30 min), `jahjah-web-truth` (Mon), `jahjah-web-backup` (nightly), `jahjah-web-backup-check` (Mon), `ci` on every PR, **Codex** reviewing every PR, and the Claude `review` job only when someone dispatches it. The registry is `jahjah-internal/docs/runbooks/automations.md`; the current list is `docs/STATE.md` §1.

**The owner is a relay, not a reviewer.** Plan for your rigor and the implementer's compliance. He reads headlines, thinks in visuals, and decides — never ask him to review code or a plan; interpret, then bring him A/B with a recommendation.

### The chunk loop

```
STRATEGIST opens a GitHub ISSUE in obidex/jahjah-website carrying the whole chunk plan,
           labelled chunk:proposed (+ model:opus or model:sonnet)
   → OWNER adds chunk:approved         (his label IS the confirmation — one tap, from anywhere)
   → jahjah-web-dispatch picks it up within 2 minutes, relabels chunk:running, and starts the
     chunk on the executor in tmux `web` with CHUNK_ISSUE set
   → CLAUDE CODE runs unattended: tasks → PRs → CI → reviewer → merge (if pre-authorized)
   → CLAUDE CODE reports as COMMENTS on that issue, and to the relay, moves the label to
     chunk:done (or chunk:blocked), and on `final` CLOSES THE ISSUE
   → STRATEGIST reads the issue, the PRs and CI through its GitHub connector; verifies prod;
     reports ONCE
```

**The chunk issue is closed by the final report, not by a person** (W127). `/relay-report final` adds
`chunk:done`, removes `chunk:running` and then closes the issue; `blocked` and `interrupted` leave it
open, because they need a person and an open issue is how they ask. The owner never closes one by hand.

**The owner's label replaced his paste** (W099). It no longer requires him at a desktop with tmux
open at the moment the plan is ready, and the plan, the approval, the work and the result end up as
one thread in the repository the work changes.

No mid-chunk pings. The only mid-chunk messages to the owner are **BLOCKED**, **stall**, or **final**.

**A chunk started by hand still works** — paste the prompt into tmux `web` as before. It differs in
exactly two ways: `CHUNK_ISSUE` is unset so reports go only to the relay, and the executor is asked
to approve the relay-publish wrapper once (W103, point a).

**Three facts about the two kinds of session, learned in P1 and worth planning around (W116, W117).**
A **dispatched** session cannot edit `.claude/**` at all — the harness refuses it whatever the
allowlist says — so an allowlist gap is fixed by an **interactive** session and never by the chunk
that hits it, and a plan that pre-authorizes editing `.claude/settings.json` is pre-authorizing
something the executor cannot do. A dispatched session also runs only what the allowlist names, so
the allowlist must be **exercised by a dry run** before a chunk depends on it. And an **interactive**
session that goes quiet is usually sitting on a permission prompt rather than crashed: the owner
presses through it or re-pastes the chunk. Neither kind of stall shows up as a failure anywhere —
the dispatch lane's label fallback only fires when the process actually exits.

**What the lane does when a chunk fails to say so.** When the chunk's process exits, the lane reads
the issue's labels: still `chunk:running` means no final report was ever posted, so it comments the
exit code and relabels `chunk:failed`. A chunk that finished properly has already moved the label
itself. **The label is the machine-readable verdict; the report is the human one.**

| Label | Means |
|---|---|
| `chunk:proposed` | the strategist has written the plan; waiting on the owner |
| `chunk:approved` | the owner's confirmation. The lane starts it within 2 minutes |
| `chunk:running` | dispatched and running now |
| `chunk:done` | the chunk posted a final report |
| `chunk:blocked` | the chunk stopped and needs a decision, or hit a cap |
| `chunk:failed` | the process exited without the chunk ever reporting — a crash, a kill, a cap on the lane |
| `model:opus` / `model:sonnet` | routing; `opus` is the default when neither is set |

The lane's own description, caps and kill switch live in `jahjah-internal`'s
`docs/runbooks/automations.md` under `jahjah-web-dispatch`. **Kill switch:
`touch /opt/jahjah/WEB_DISPATCH_OFF`.**

### Chunk plan — required contents

Tasks in order (each independently shippable) · expected issues + contingencies · caps (time, retries) · stop-conditions · which Tier-3 files may be touched (named explicitly) · GATE-2 pre-authorization scope · what a BLOCKED report must contain. **No check-cadence tier** — that field died with the MEGA/MID/SMALL table below; the cadence is now the same for every chunk.

### Check cadence (owner rule, 2026-09-03)

**Once per hour, at most 3 checks per awaited chunk.** This replaces the MEGA / MID / SMALL table
that stood here, which scheduled up to six checks on a tightening interval — the tiering bought
nothing a chunk's own reports did not, and the fast early checks mostly found a chunk still
running.

**At most 3 chunks per day per lane.** The lane shares one Claude Max pool with the ERP, so a
fourth chunk here is a chunk the other lane does not get. The scarce resource is weekly usage
headroom, not tokens.

A check that finds a new relay report resets the counter. Cap hit → evidence sweep first (relay
INDEX, HEALTH-daily, Vercel deployments, CI), then ONE no-alarm note saying what was verified, then
stop scheduling.

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
- **THIS IS NOW MACHINE-ENFORCED, NOT DISCIPLINE (W100).** A ruleset on `master` requires a pull request, allows **squash only**, requires the status check **`ci`**, forbids deletion and non-fast-forward, and has **no bypass actors** — the admin who created it cannot bypass it either. **Never edit or delete it**; if it blocks a PR because `ci` is red, fix the PR. Its id and the evidence that it bites live in `docs/STATE.md` §1 and in W100, because both are dated facts.
- **Pre-authorizable per chunk:** the plan states which PRs may merge themselves. Fires only on clean reviewer AND green CI; either failing re-opens the gate. Covers same-scope follow-ups.
- Tier-3 files (§3) merge only when the chunk plan named them — and CI's **`tier3-guard`** step now enforces the machine half (W101): a PR touching a Tier-3 path must carry a body line `Tier-3: authorized by chunk <name>`. It is a floor, not a ceiling: the judgment half of that list is not path-expressible, and you still decide whether the named chunk plausibly authorized *those* files.
- **The reviewer of record is Codex** — the ChatGPT GitHub app, posting as `chatgpt-codex-connector` on the owner's ChatGPT plan, reading `AGENTS.md` at the repo root. It is a second pair of eyes from a different model family at zero Claude spend, and **not** a required check — the executor's reviewer subagent stays the gate. Address every finding of its that meets THE BAR before merging. The Claude `review` job (`.github/workflows/claude-review.yml`, judged by `REVIEW.md`) is now **`workflow_dispatch`-only**, the fallback when Codex is down or silent. Silence from either is never approval, and per W105 Codex is an external system: a dated assumption, not a property of ours.
- **Verify merge and deploy yourself** (Vercel MCP `list_deployments` for project `jahjah-website`; live probes). Never ask whether it deployed. PR-green ≠ main-green: watch the post-merge run.
- The Sanity publish webhook is a second path to production with no commit. It is watched by the weekly TRUTH report, never gated.

### THE BAR — what blocks a merge

Only: a public visitor can obtain a price, stock quantity, customer data or a token (via HTML, JS, API or build output); a hidden product reaches static HTML; **an Arabic string not approved by the strategist ships**; a Tier-3 file changed outside the plan; a broken build.

**That Arabic clause was amended by the owner on 2026-09-04 (W125), and the change is deliberate.** It used to read "an unreviewed Arabic string ships", which made the native reviewer a merge gate — and the cost was measured: PR #34 sat open for a day holding an accessibility fix the site needed. **Arabic translation must never block a merge.** AI-drafted Arabic that the strategist has approved ships; the native reviewer does a **batched mass review** afterwards, tracked by a standing row in ROADMAP's register naming the date from which strings are pending. The gate is not waived, it is moved off the critical path — and every shipped string is still listed, with the specific questions it raises, in the PR that shipped it. Everything else — conventions, coverage, polish — becomes a dated follow-up in `docs/DECISIONS.md` and a row in ROADMAP's register.

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
(Delivered as a GitHub issue labelled chunk:proposed — §1. The body below IS the issue body.)

PREFLIGHT (stop with a BLOCKED report if any fails):
- pwd = /opt/jahjah/web (or the clone path in STATE) · git fetch · HEAD == origin/master · tree clean
- `git ls-files | wc -l` recorded, and RECOUNTED before every push (W092 — a .gitignore rule can
  silently drop files from a commit and `git add` reports nothing)
- gh auth status OK · git ls-remote origin OK · npm ci clean · npm run build exit 0
- env names present (never print values): PUBLIC_SANITY_PROJECT_ID, PUBLIC_SANITY_DATASET, SANITY_READ_TOKEN[, SANITY_WRITE_TOKEN, SUPABASE_*]
- read docs/STATE.md and docs/reference/site.md; list every assumption in this prompt that contradicts them

TASKS (each = branch → PR → CI → reviewer → merge if pre-authorized → next):
T1 … Tn — scope, files (Tier-3 files named), acceptance checks (compiled-output greps, page count, probes)

RULES: CLAUDE.md is binding. Every user-facing string via translations.js + AR mirror; AR strings drafted only if the plan says so and marked "AR: pending native review" in the PR. No new dependency unless listed here. No secrets in output or reports.

GATE 2: PRs T1–Tk may self-merge on clean reviewer + green CI. Tier-3 PRs: <list or "none">.
CAPS: <hours>, <retries per task>, build timeout. STOP-CONDITIONS: <...>.
REPORTS: progress report after each merged PR; final report at end; BLOCKED report on any stop. Publish via /relay-report — which posts to the chunk issue AND the relay, and moves the issue's label.
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

**Content and language (W022–W025, W042, W056, W060, W010).** Company name `شركة الجحجاح التجارية` exact; `JAHJAH` is a brand, Latin in both languages; brand order DCEL → LAPON → JAHJAH → SUNNY → DSP in `BRAND_ORDER`; Arabic canonical; every meaningful AR string strategist-approved before it ships and native-reviewed in the batch afterwards, meta included (W125); MSA register; positioning manufacturer + supplier + distributor.

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

---

## 8. THE STRATEGIST'S OWN LANE — how this chat reaches the project

The strategist chat now has a **GitHub connector** (OAuth app "Claude strategist"): it can **read the
repository, its pull requests and its CI**, and **write issues and comments**. It still has no `gh`,
no shell and no credential of its own, and the connector cannot push, cannot merge and cannot change
a setting — blocked at the connector, and blocked again at the `master-protection` ruleset even if it
could try.

| Direction | Channel | Limit |
|---|---|---|
| **In** — repo, PRs, CI, issue threads | the GitHub connector | read-only on code; no push, no merge |
| **In** — canon, reports | the public relay mirror (READING MAP) | read-only; lags `master` by ≤ 30 min |
| **Out** — work | **one GitHub issue per chunk**, labelled `chunk:proposed` | the OWNER's `chunk:approved` label is what starts it (§1) |
| **Out** — questions, corrections | a comment on the chunk's issue | the executor may not see it mid-chunk; it is a record, not a channel |
| **Both** — a connected folder | Cowork device tools on the owner's laptop clone | may edit and commit there; **the push stays his**, and **GATE 2 still binds** (§2). Canon updates are not this path's work: they stay with the implementer, in the chunk's last PR (§7) |

**Reports are dual-published.** Every report goes to the issue *and* to `jahjah-website/reports/`.
Whether the relay has been retired yet is a state fact — `docs/STATE.md` live flags and ROADMAP F26 —
and until it is, the relay is the fallback and the thing the check cadence in §1 watches.

### Reading the canon

Plain `WebFetch` is the reader; the URL, the `?v=` cache-buster and the mirror's lag are in the
READING MAP. What that entry does not say:

| Source | Verdict |
|---|---|
| the raw mirror (+ `?v=`), and `INDEX.md` | the readable surface for canon |
| the GitHub connector | now the readable surface for **code, PRs, CI and issues** — it is fresher than the mirror and it is not rate-limited the way `api.github.com` is |
| `api.github.com` unauthenticated | rate-limited; never build a workflow on it |
| `github.com` HTML | robots-blocked |

**A fresh `INDEX.md` does not mean a fresh sibling (W102).** `raw.githubusercontent.com` can serve a
current `INDEX.md` and a stale body in the same second, cache-buster on both. The freshness signal is
**INDEX.md's "Mirrored commit" line**, not the body just fetched: if the body looks older than the
commit INDEX names, re-fetch rather than conclude the canon says what the stale copy says.

And: a large file comes back **summarized, and the summary is lossy**. Never conclude a rule is absent
because a search result or an extract omitted it — open the file that owns the fact and read the
section. Absence from an extract is evidence of nothing.

### The claude.ai project knowledge

A **GitHub sync of this repo's `docs/` + `CLAUDE.md`** (W098) — a searchable snapshot, useful for
orientation and for finding which file owns a fact. It is not the canon: it lags, the mirror is
fresher, and **the mirror wins**. Never upload a copy of a canon file to the project.

### What the guardrails actually guarantee

- **`.claude/settings.json` is a guardrail against accident, not a sandbox (W095).** Deny rules match
  command patterns, so a path around any one of them exists. What holds now is machine-enforced:
  `master-protection` (W100) lets nothing reach `master` except a squash-merged PR on green `ci`, with
  no bypass actors — the admin who created it included.
- **Allow-rules load only after a human trusts the workspace once.** Whether that has been done, and
  for which clone, is a state fact — `docs/STATE.md` §2, not here.
- **Some things a permission rule cannot reach at all (W103, point a).** Claude Code refuses `.`/`source` as
  shell-code evaluation whatever the rules say. A plan that assumes "we can allow it" should be
  checked against that.
- **A repo `.gitignore` can silently drop files from an owner push (W092).** `git add` reports
  nothing; the branch simply lands without them. **Every preflight counts files**, before and after.

### Owner rules for this lane

- **Rephrase his request before acting.** Say back what you understood and act on that reading — it is
  how a misread surfaces while it is still cheap.
- **When he connects a folder, the work happens in that folder** — not in the cloud container, and not
  as text he has to place by hand. The loop and the gates are unchanged by where the keystrokes land.
- **One-time information goes in chat, never a new file.** §5 states this in full; it is repeated here
  because a connected folder makes writing the file the path of least resistance.
- **An issue is not a chat.** The chunk issue is a record the owner may read on a phone: put the
  headline first, the plan below it, and nothing in it that only makes sense to the executor.
