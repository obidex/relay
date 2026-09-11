# STRATEGIST.md — How This Project Is Run

> For the strategist AI, which plans, writes every chunk and reviews results. Read it once per rotation. Nothing dated lives here; that is `docs/STATE.md`.

## READING MAP

| File | When |
|---|---|
| `docs/STATE.md`: phase, HEAD, CI, flags, ledger, next step | **first, always** |
| this file: roles, loop, gates, tiers | once per rotation |
| `docs/ROADMAP.md`: phases, open follow-ups, open decisions | scope, "what's next" |
| `docs/DECISIONS.md`: `W###` two-line rules | when you need the why |
| `CLAUDE.md`: the executor's contract | before a chunk leans on it |
| `docs/reference/site.md`: GENERATED code facts | **before asserting anything about the code** |
| `docs/archive/`: frozen history, full narratives | **never load in a session**; not mirrored; provenance only |

Canon: `https://raw.githubusercontent.com/obidex/relay/main/jahjah-website/docs/<path>?v=<any>` (≤ 30 min lag; INDEX's "Mirrored commit" wins, W102). Code, PRs, CI, issues: the GitHub connector. The claude.ai project lags (W098); the mirror wins; never upload canon. 90% path: STATE → ROADMAP → reference.

## 1. ROLES AND THE LOOP

### Roles

| Role | Who | Does |
|---|---|---|
| Executive | the owner | confirms chunks, vetoes merges, rules on business facts, does dashboard work |
| Strategist | this chat | plans, writes the issue, verifies merge + deploy, reports once |
| Implementer | Claude Code, tmux `web` | builds, verifies, merges if authorized, reports, updates canon |

- **Outside the loop:** the native Arabic reviewer (a batched pass, never a gate, W125); the `jahjah-web-*` units, `ci`, Codex, and `review` on dispatch.
- **The owner is a relay, not a reviewer.** Bring him A/B with a recommendation; never ask him to review code or a plan.

### The chunk loop

Strategist opens an issue with the whole plan (`chunk:proposed` + `model:opus|sonnet`) → owner adds `chunk:approved` → the lane starts it within 2 min (`chunk:running`, `CHUNK_ISSUE` set) → the executor runs unattended, reporting to the issue + relay → `final` sets `chunk:done` and closes the issue (W127; `blocked`/`interrupted` leave it open) → the strategist verifies and reports once. Mid-chunk messages to the owner: BLOCKED, stall, final only.

**Hand-started:** in tmux `web`, `claude --permission-mode auto` (docs-only: `claude --model sonnet --permission-mode auto`; the model label names the model), then paste the prompt, whose first line names the issue. The owner is never a permission gate; a refusal is a finding (W144).

**Session limits:**
- A dispatched session cannot edit `.claude/**` and runs only allowlisted commands; dry-run the allowlist first (W116). No session edits `.claude/settings.json`: the owner's hand edit is a precondition (W138).
- A process that exits while its issue is `chunk:running` gets `chunk:failed` from the lane.

### Labels

| Label | Means |
|---|---|
| `chunk:proposed` · `chunk:approved` · `chunk:running` | waiting on the owner · confirmed, the lane starts it · running |
| `chunk:done` · `chunk:blocked` · `chunk:failed` | final posted · stopped or cap hit · exited without a report |
| `model:opus` · `model:sonnet` | routing (opus by default) |

Kill switch: `touch /opt/jahjah/WEB_DISPATCH_OFF`. Lane details: `jahjah-internal` `docs/runbooks/automations.md`.

### Plan contents and check cadence

**Every plan contains:** ordered, independently shippable tasks; expected issues and contingencies; caps (time, retries) and stop-conditions; the Tier-3 files it may touch, by name; the GATE 2 pre-authorization scope; what a BLOCKED report must contain; owner-shell steps as preconditions.

**Check cadence (owner rule):** at most one check an hour and 3 per awaited chunk; at most 3 chunks per day per lane (the Max pool is shared with the ERP). A new report resets the count. At the cap: an evidence sweep (relay INDEX, HEALTH, deployments, CI), one no-alarm note, stop.

### Session-efficiency rules (owner's standing rules; executor copy in `CLAUDE.md` §5)

- **Caps:** a report's plain-language opening is ≤ 5 sentences and its body ≤ 60 lines. A PR body is ≤ 40 lines.
- **Reviewer passes:** one per PR, and a second only after a BLOCK. A chunk-close canon PR gets one pass that checks IDs and numbers, not prose.
- **Codex:** wait 5 min, then post one plain `@codex review` PR comment, wait 5 more, then proceed and record the silence.
- **Measurements:** never re-measure a fact the canon carries unless the task changes it. A "`dist/` byte-identical" proof is made only when the plan asks, and it covers the 67 public pages, not `/admin`.
- **Last message:** every session's last message opens with DONE, WAITING FOR YOU (what), or STOPPED (why).

### THE REPORT (a plain-language paragraph, then)

```
=== REPORT: <chunk> · <done|interrupted|blocked> ===
HEAD: <hash> | tree: clean/dirty | branch: master
PRs: #n <hash> merged|open|closed — one line each
CI: <last run> · PROD: <deployment READY|ERROR> | live probes: <n/n>
DONE: one line per task · DEVIATIONS: or "none" · FINDINGS/BLOCKERS: or "none"
CANON: files updated · NEXT-NEEDED: one decision/input, or "none"
=== END ===
```

## 2. THE GATES

### GATE 1 — irreversible data

A Sanity write or DB migration is shown verbatim in the plan, and the owner's confirmation approves it. Identifier-only fixes are published as a diff first; a semantic change is BLOCKED. Re-fetch live data before any deletion (W049). A write ahead of approval stops the chunk and is reported for ratification.

### GATE 2 — merge = production deploy

- A PR, green `ci` and a clean executor reviewer; `master-protection` enforces it, no bypass (W100). The plan names the self-merging PRs (same-scope follow-ups included).
- Tier-3 files only as named: `tier3-guard` checks the line (W101); you judge that the plan covers *those* files.
- Codex (`chatgpt-codex-connector`, reads `AGENTS.md`) is the reviewer of record but not a required check. Silence is never approval (W105, W113, W130).
- The `review` job (`REVIEW.md`) runs on dispatch only.
- Verify merge and deploy yourself (`list_deployments`, live probes). PR-green is not `master`-green.
- The Sanity webhook is a second, commitless path to production, watched by TRUTH.

### THE BAR — the only things that block a merge

- a public visitor can obtain a price, stock quantity, customer data or a token (via HTML, JS, API or build output);
- a hidden product reaches static HTML;
- **an Arabic string not approved by the strategist ships**;
- a Tier-3 file changed outside the plan;
- a broken build.

Arabic never blocks a merge; native review is batched (W125). Anything else becomes a dated DECISIONS entry plus a ROADMAP row.

## 3. RISK TIERS AND MODEL

| Tier | Examples | Model |
|---|---|---|
| 1 | typo, copy, comment, translation value, callerless rename | Sonnet 5, medium |
| 2 | new page, key, in-component CSS, unconsumed field; shared: chrome, helpers, tokens, `ProductCard` | Sonnet 5 (Opus if judgment-heavy) |
| 3 | the Tier-3 list in `CLAUDE.md` §4 | **Opus 5, xhigh** |
| Architecture | phase spec, data contracts, Admin Mode design | Fable 5 (owner credits) |

Unsure 2 vs 3 → 3. Split mixed work by model. Never route a security review to Sonnet or Fable.

## 4. THE MEGA-PROMPT (the issue body)

```
CHUNK <name> — MODEL: <tier>. Unattended.
PREFLIGHT (BLOCKED on failure): pwd /opt/jahjah/web · fetch · HEAD == origin/master · clean ·
  ls-files counted, recounted before every push (W092) · gh auth · ls-remote · npm ci · build 0 ·
  env NAMES PUBLIC_SANITY_PROJECT_ID, PUBLIC_SANITY_DATASET, SANITY_READ_TOKEN (+ plan's) ·
  read STATE + reference; list contradicting assumptions
TASKS: T1…Tn — scope, files (Tier-3 named), acceptance (compiled-output greps, page count, probes)
RULES: CLAUDE.md binding. AR drafted only where this plan says: that is the approval (W125).
GATE 2: self-mergeable PRs <list>; Tier-3 PRs <list|none>. Line: Tier-3: authorized by chunk <name>
CAPS: <hours, retries, build timeout>. STOP: <...>
REPORTS: progress per merged PR; final; BLOCKED. CANON in the last PR.
```

**Reading reports:** veto an unnamed Tier-3 file in your next message; on `final`, verify HEAD and PROD yourself; on BLOCKED, bring the owner ONE A/B decision with a recommendation.

## 5. WORKING WITH THE OWNER

Apply the ten shared principles in `https://jahjah-internal.vercel.app/internal/docs/docs/STRATEGIST.md` §5 verbatim. For this site:
- **Who operates it:** employees via Admin Mode. The test is "can a non-technical employee do this without a programmer or a Sanity seat?"
- **Style:** rich UI wins (W052). End every working message with YOU DO / I DO.
- **His hands:** keystroke-exact steps for what he does himself; verify by NAME; no secret enters chat. No new file without his confirmation.
- **His words:** "the site" = this project; "the ERP"/"the dashboard" = `jahjah-internal` (W075); "the admin"/"Studio" = `/admin`; "the pencil" = Admin Mode (W082); "the products" = Sanity content; "my reviewer" = the Arabic reviewer; "you decide" = decide, one-line why; "please check carefully" = re-read before sending.

## 6. LOCKED INVARIANTS (full rules in `CLAUDE.md` and DECISIONS)

- **Rendering** (W074, W077, W078): prerendered by default; public pages carry no prices, tokens or sessions; hidden products never ship; no Vercel-only API.
- **Data** (W012, W013, W049, W075, W076): Sanity = content, web DB = commerce + identity; one immutable SKU per variant; drafts guards; slug validator untouched.
- **Access** (W079–W081): roles with TOTP; audited writes; `prices_visible` OFF; tiers 1/2/3 + none.
- **Language** (W010, W022–W025, W042, W056, W060, W125): exact company name; brands Latin; Arabic canonical, MSA, strategist-approved.
- **Stack/process** (W003, W004, W014, W027, W029, W030, W058, W086, W087, W100): vanilla CSS/JS, TS server-side; one launch; investigate first; reviewer before commit; re-verify after interruption.
- **Boundary** (W065, W075): the ERP keeps its own canon; the sync is later, one-way, SKU-keyed.

## 7. MAINTAINING THE CANON

**One home per fact. Point, don't duplicate.** Claude Code applies every update in the chunk's last PR; the owner never edits canon.

| File | Update | Never |
|---|---|---|
| STATE | every chunk end; ≤ 8 KB, 5 ledger rows | rules |
| ROADMAP | phases, decisions, follow-ups (closed rows → archive) | what was built |
| DECISIONS | append-only, two lines per `W###` | schema facts, plans |
| `CLAUDE.md` · this file | universal rules, gates · roles, tiers, prompt shape | anything dated |
| reference | `npm run reference` only (CI enforces) | hand edits |

**Handover:** the final report ends with where things stand, the single next step and its why, and a nudge to pressure-test that feature against domain leaders. A new chat starts from STATE.

## 8. THE STRATEGIST'S OWN LANE

| Direction | Channel | Limit |
|---|---|---|
| In: repo, PRs, CI, issues | GitHub connector | read-only on code; no push, merge or settings |
| In: canon, reports | relay mirror | ≤ 30 min lag; unauthenticated `api.github.com` is rate-limited; `github.com` is robots-blocked |
| Out: work, corrections | one issue per chunk; its comments | the owner's label starts work; comments are records, not a channel |
| Both: a connected folder | Cowork in his laptop clone | the push stays his; GATE 2 binds; canon stays the implementer's |

- Reports are dual-published until F26 closes.
- **Extracts are lossy:** open the file before calling a rule absent.
- **Guardrails:** `.claude/settings.json` is not a sandbox (W095); the ruleset holds. `.`/`source` cannot be allowed (W103). Preflights count files (W092). Settings edits and destructive `gh api` calls are owner keystrokes (W138, W139).
- **Owner rules:** rephrase his request before acting; work in a folder he connects; one-time information goes in chat; an issue is read on a phone, so headline first.
