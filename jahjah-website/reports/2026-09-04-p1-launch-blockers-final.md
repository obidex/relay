**P1 is closed, and the site is in better shape than it was — but P1's own exit is only partly
met, and the two things missing are yours, not the executor's.** Every code item shipped: no
broken image requests anywhere, `/admin` out of the sitemap, the 404 page no longer promising
pages that do not exist, the accessibility audit's findings fixed, and About / Brands / Contact
each with their own description instead of one shared line that called the company an "authorized
distributor" — a claim you have not ruled on. What did **not** happen is anything only a person can
do: nobody has looked at the three washing-machine photos to say whether they carry a stock-photo
watermark, and nobody has said which of the 22 placeholder products should survive. One pull
request is deliberately left open for the Arabic reviewer.

Two things went wrong in the running of it, and both are written down rather than smoothed over.
The dispatched run died an hour in on a permission list nobody had tested; two later sessions ended
silently, one of them after finishing work it never reported. And this chunk's own closing document
was about to record something false about the code reviewer — the review pass on it caught that,
which is the single most useful thing that happened today.

=== REPORT: P1-launch-blockers · final ===
HEAD: 646e887 | tree: clean | branch: master | git ls-files: 77

PRs:
  #25 c1cd217 MERGED — T0: Codex becomes reviewer of record; Claude review → manual fallback
  #26 09bc943 MERGED — T2: a missing image is null → an inline no-image tile, never a 404
  #28 6e28bc3 MERGED — T3: assert no hidden product reached the build
  #29 6fbe570 MERGED — T1: make the commands the canon documents actually runnable headless
  #30 cc1d89b MERGED — T4: /admin out of the sitemap; the Studio marked noindex
  #31 3c30b37 MERGED — T5: the 404 page's head, and a toggle that points at a real route
  #32 7afc84a MERGED — T6: accessibility — pressed state, headings, AR labels, LTR isolation
  #33 a0f8bff MERGED — T7: page-specific meta descriptions for About, Brands, Contact
  #35 646e887 MERGED — T8: canon and chunk close
  #34 ac8ba47 **OPEN ON PURPOSE** — the last two Arabic accessible labels. Three unreviewed
      Arabic strings, so GATE 2 excludes it. `ci` green. **It must not be merged until a native
      speaker has ruled on the strings.**

CI: every PR green. This session's runs — #31 33843765568 · #32 33848398041 · #33 33850776149 ·
    #34 33882... (green) · #35 33883186214. Post-merge master green each time; the last is
    33883361698, completed 14:24:03Z.
PROD: master deployment READY, verified by fetching live pages rather than reading a dashboard.

LIVE PROBES — 18/18 as expected (the two 404s are the expected answer, not a failure):
  / 200 · /ar/ 200 · /about/ 200 · /ar/about/ 200 · /brands/ 200 · /ar/brands/ 200 ·
  /contact/ 200 · /ar/contact/ 200 · /products/ 200 · /ar/products/ 200 · /how-to-buy/ 200 ·
  /ar/how-to-buy/ 200 · /brands/dcel/ 200 · /ar/brands/dcel/ 200 ·
  /products/dcel-washer-front-7kg/ 200 · /admin/ 200 · /nonexistent-path/ 404 ·
  /ar/nonexistent-path/ 404

HEADING AUDIT: `OK heading hierarchy: no skipped levels across 67 built pages`. Before T6 the two
products listings went h1 → h3; `scripts/heading-audit.mjs` now runs from `verify.sh` and exits 3
for a finding, 1 for its own failure, so a crashed script cannot be reported as a heading defect.

AR ACCESSIBLE LABELS — on master, i.e. WITHOUT #34:
  | label | count | source |
  |---|---|---|
  | تصفّح الموقع | 99 | nav.menuLabel (existing) |
  | فتح القائمة | 33 | nav.openMenu (existing) |
  | تواصل معنا عبر واتساب | 33 | brandPage.contactWhatsapp (reused, T6) |
  | Jahjah Trading Company — الرئيسية | 33 | site.companyName + nav.home (T6; Latin half is the visible text, WCAG 2.5.3) |
  | الفئة | 1 | products.category (reused, T6) |
  | اللون / أبيض / رمادي / ستانلس ستيل | 3/1/1/1 | existing colour strings |
  | **Breadcrumb** | **27** | **STILL ENGLISH — needs a new AR string → PR #34** |
  | **View image N** | **22+2** | **STILL ENGLISH — needs a new AR string → PR #34** |
  With #34 merged, every rendered accessible name under `dist/ar/` is Arabic.

DONE:
  T0 — reviewer of record moved to Codex; `claude-review.yml` → `workflow_dispatch` only; AGENTS.md
       added; ci.yml's stale header fixed; the generator now walks `.github` whole. (F25, F28, F29,
       F30 closed.)
  T1 — allowlist repaired (#29). Five traps measured and written into CLAUDE.md §9, including that
       a rule ending at a `VAR=` assignment is a universal bypass of the entire deny list.
  T2 — a missing image is `null` and renders an inline tile emitting no `<img>` at all. 126
       placeholder references → 0. No asset 404s (W112).
  T3 — every product query was already fail-closed on `published == true`, so the GROQ half was
       "no work needed" (W029); what shipped is the missing guard (W110).
  T4 — `/admin` out of the sitemap (66 URLs) and marked `noindex, nofollow`, asserted independently.
  T5 — the 404 page: `noindex`, no canonical, no hreflang, toggle to the other language's home, and
       the document root stays English so the English chrome is not announced as Arabic (W111).
  T6 — accessibility, PARTIAL BY DESIGN (see #34).
  T7 — three distinct EN descriptions (150/138/148 chars); each AR mirror byte-identical to an
       existing reviewed string; the unruled agency claim gone from every meta description.
  T8 — canon: W110–W122 appended, STATE rewritten, ROADMAP P1 statused and F33–F41 opened,
       CLAUDE.md §7 and STRATEGIST §1 updated.

VERIFY: 0 FAIL · 1 WARN · 67 pages. The WARN is pre-existing F5 (two dead RTL rules, P5).

DEVIATIONS:
  - T3's GROQ change was NOT made: the plan's `published != false` is wrong in the one direction
    that matters — it admits an *unset* field. Ratified by the strategist mid-chunk; W110.
  - T6(e) ships `<span><bdi dir="ltr">` where the plan says `<span dir="ltr">`, because
    `.branch-number` is a flex item and `text-align: start` resolves against the element's own
    direction. And every spec value is wrapped, not only the ASCII-only ones.
  - T6's AR-label acceptance is not met on master, by the plan's own split rule → #34.
  - T7 is labelled "Tier 1–2" by the plan and is Tier 3 in fact; `defaultDescriptionAr` was changed
    although T7's removal clause is EN-only (it reuses an existing string, no new Arabic).
  - Files touched outside a task's named list, each named in its PR: `NoImageTile.astro` (comment),
    `Layout.astro` footer copyright, `ProductCard.astro` image alt, both `brands/[slug].astro`.
  - CARRIED TO P1.1, unchanged: T0d (dependency updates — `npm update` and `gh pr close` forbidden
    this chunk) · the T1 visual watermark verdict (owner's) · #27 (untouched, as instructed).
    The probe table deviation is CLOSED — the table above is real.

FINDINGS:
  1. **A §5 BREACH NOBODY HAD NOTICED, and it is the most important thing in this report.** PR #28
     was merged **88 seconds** after it opened; Codex's review arrived **108 seconds after the
     merge**, carrying **two P1 findings that were never seen and are still unfixed**. One — the
     hidden-product check exits 0 with a SKIP on any Sanity failure, so `ci` stays green having
     never checked — was independently rediscovered at chunk close and written up as F37; the
     register now says Codex found it first. The other — `fromDist` is null when the build emits no
     Sanity image URL, so the script silently falls back to `pxf1amia/production` and would check
     the wrong store — was recorded nowhere. Both are now **F40**, and both have been answered on
     #28 itself rather than left silent. **Neither meets THE BAR**, so nothing was bypassed.
  2. **THIS CHUNK'S CLOSING DOCUMENT WAS ABOUT TO RECORD SOMETHING FALSE ABOUT THE REVIEWER.** Its
     first draft claimed Codex reviewed 2 of 6 PRs, stayed silent after an explicit `@codex review`,
     and had never labelled a finding. All three were false. Measured: **4 of the 10** (#26 P2,
     #27 P1, #28 2×P1, #31 2×P2), it **answered in 4m23s**, and it has labelled P1/P2 since #22 and
     #23 on 2026-09-02. The cause is mechanical and is now canon: **Codex speaks on three surfaces**
     — the review (boilerplate), the inline review comments (the findings, which the review body
     does not list), and an **issue comment** when a re-review finds nothing. Reading two of three
     reports silence that is not there. W113 corrected, W122 records the lesson.
     Codex reviews: #26 pullrequestreview-5097399643 · #27 -5097542137 · #28 -5097561696 ·
     #31 -5106277046 · plus issue comments on #31 and #35.
  3. **Codex reviewed the chunk-close PR itself and was right.** It found P1.1's Dependabot scope
     contradicting itself — "all six" in one row, "not the two majors" in the next. Fixed in
     `abffd70`; the six now split four (P1.1) and two (F34's own named chunk) everywhere.
  4. **Nine executor reviewer passes across the chunk; four changed the shipped result** — an
     accessibility *regression* in T5 (the root was being flipped to Arabic under English chrome),
     a flex/`text-align` bug in T6, a false verification committed into source in T7, and an
     **`innerHTML` injection that T6b's own localization created** (W120: localizing a hardcoded
     string changes who controls its bytes).
  5. **F11 is closed and the answer is "partly".** The reviewer subagent's `tools:` frontmatter
     DOES restrict — four passes each held exactly `Read` and `Bash`, no MCP tools at all — but
     **not to what it lists**: `Grep` and `Glob` are named and were not granted, and the
     per-command `Bash(...)` scoping is not enforced. A `tools:` list containing `Bash` is not a
     read-only guarantee.
  6. **A bare `git push` is refused by the deny list** even on a chunk branch. Correct behaviour —
     the rule exists to stop a push to `master` and cannot know the branch — but it means the
     canonical spelling must always name the remote and branch. Reporting it per CLAUDE.md §9
     rather than routing around it silently.
  7. **The clock.** The dispatched run exited 1 at 5307 s. Two interactive resumes followed and
     **both ended silently**; the first had pushed T5, gone green and taken the Codex review but
     never merged #31 or reported. This session was itself paused ~5.5 h mid-task and resumed under
     W058 — `npm ci`, build and verify re-run from scratch before anything was merged. F39.

CANON updated in #35: `docs/DECISIONS.md` (W110–W122 appended, 0 deletions) · `docs/STATE.md`
(HEAD, live flags 2/3/4/9/10 rewritten, 12 and 13 new, ledger row, §4 owner items, §5 next step) ·
`docs/ROADMAP.md` (P1 per-item status and exit verdict; F1 narrowed; F9, F11, F23, F24, F25, F28,
F29, F30 closed; F33–F41 opened) · `CLAUDE.md` (§7 shared-box prohibitions, the corrected Codex
paragraph, "waiting means waiting") · `docs/STRATEGIST.md` (the MEGA/MID/SMALL cadence table
replaced by the owner's once-per-hour / max-3-checks / max-3-chunks-per-day rule; the chunk loop
gains W116 and W117's session facts) · `docs/reference/site.md` regenerated.

NEXT-NEEDED — three things, all yours, and two of them gate P1's own exit:
  1. **Look at the DCEL washer's three photographs and say whether they carry a watermark** (F1).
     The grep half is clean; this is the half a machine cannot do.
  2. **Say which of the 22 placeholder products should survive** (F35). W031 wants 8–10 real
     flagships. The machinery to hide the rest exists and is proven; nobody has said which.
  3. **Rule on the launch facts** (W088) — and this one has become self-inconsistent while
     unanswered. P1 removed "authorized distributor" from every meta description in both
     languages, but the same claim still stands in the home page's visible copy and in the
     structured data that feeds knowledge panels. **The site currently says two different things
     about itself.** Both remaining places need a new Arabic string, so they move together, after
     you rule.
  Also waiting on a person, but not on you: **PR #34 needs the native Arabic reviewer** on three
  drafted strings — مسار التنقل, عرض الصورة {n}, الخيار {n} — with specific questions in its body.

HANDOVER — what the next session should know:
  - **Next chunk is P1.1** (F33): the four minor/patch Dependabot PRs (#14, #15, #16, #19 — three
    are security), close #27, the T1 visual verdict, and nothing else. **#20 and #21 are NOT
    P1.1's** — they are major Studio bumps and need their own named chunk (F34).
  - **#34 is open and must stay open** until the Arabic strings are ruled on. It regenerates
    `docs/reference/site.md`, and #35 has since regenerated it on master, so **re-run
    `npm run reference` on that branch rather than resolving the conflict by hand** (F41 names the
    same class of trap for the relay-report skill).
  - **F40 first if anything touches `hidden-products-check.mjs`** — two unaddressed P1 findings on
    the guard that exists to prevent a price/visibility leak.
  - **Read all three Codex surfaces.** `gh pr view <n> --json reviews`, `gh api …/pulls/<n>/comments`
    and `gh api …/issues/<n>/comments`. Two of three is how this chunk twice reported silence that
    was not there.
=== END ===
