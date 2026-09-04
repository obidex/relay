=== REPORT: P1-launch-blockers · progress ===
HEAD: 7afc84a | tree: clean | branch: chunk/p1-t7-meta (T7 in flight) | git ls-files: 77

PRs:
  #32 7afc84a MERGED — T6: accessibility. aria-pressed on filters and gallery thumbs, heading
      hierarchy with a new audit script, one accessible name per card, Arabic chrome labels,
      LTR isolation inside RTL. ROADMAP P1.6 — PARTIAL by design, see below.

CI: PR #32 green (run 33848398041, 1m35s) · post-merge master green (run 33849339378, completed
    07:38:08Z).
PROD: master deployment READY — confirmed by fetching the live pages, not the dashboard:
    live /products/ has 7 `aria-pressed` and 22 `<h2 class="card-name">`; live /ar/contact/ has
    2 `<bdi dir="ltr">`-wrapped phone numbers.

LIVE PROBES: / 200 · /ar/ 200 · /products/ 200 · /ar/products/ 200 · /contact/ 200 ·
/ar/contact/ 200 · /brands/dcel/ 200 · /nonexistent-path/ 404.

DONE — T6 (accessibility), five defects, every one measured on compiled output.

  (a) PRESSED STATE. `aria-pressed`, not `aria-current` and not a tablist: these are toggle
      buttons whose label never changes, so a screen reader announces "Cooling, pressed" rather
      than the label mutating under the user. 7 filter buttons per listing in both languages; the
      thumbs are flipped in all three places that touch thumb state, including the template string
      that REBUILDS the row after a swatch click — missing that one would have left every rebuilt
      thumb unpressed. Review caught the first version, which server-rendered a pressed button with
      no visible counterpart until JS arrived; `is-active` is now server-rendered on the same
      button.

  (b) HEADING HIERARCHY — 0 SKIPS ACROSS ALL 67 CONTENT PAGES. Both products listings went h1 → h3,
      because `ProductCard` hard-coded h3 — correct on a brand page, a skipped level on a listing
      that has only its h1. `ProductCard` gains a `headingLevel` prop defaulting to `'h3'`, so every
      existing caller keeps what it had, and the listing passes `"h2"`. New
      `scripts/heading-audit.mjs` walks the built site and runs from `verify.sh`; it exits 3 for a
      finding and 1 for its own failure, so a crashed script cannot be reported as a heading defect.
      All three exit paths were exercised, including moving the script aside to see
      `FAIL heading-audit crashed (exit 1) — this is NOT a heading finding`.

  (c) ONE ACCESSIBLE NAME PER CARD. The category card's image repeated its visible label, giving the
      link the name "Cooling Cooling" the audit found. On this build that fix is PREVENTIVE, not
      measured: no category has an icon in Sanity, so zero such images exist today. But the SAME
      defect was live one component over — `ProductCard`'s image carried `alt={name}` inside a link
      that already prints the name as a heading, measured on 22 products as
      "<name> <category> <name> <brand>". Fixed there too.

  (d) ARABIC CHROME LABELS, every one reusing a string `translations.js` already has (W056):
      logo → `nav.home` beside the Latin company name its visible text requires (WCAG 2.5.3);
      footer nav → labelled BY its own visible heading; WhatsApp float →
      `brandPage.contactWhatsapp`; filter group → `products.category`. The language switch loses its
      `aria-label` entirely — the link's own localized text IS the name, and an English override
      only made the accessible name disagree with the visible one — and gains `lang`, which is what
      makes a screen reader pronounce it correctly.

  (e) LTR ISOLATION. `<bdi>` on every spec value; `dir="ltr"` on the model number;
      `<span><bdi dir="ltr">` on the contact phone numbers; `<bdi>` around the AR footer copyright,
      where a Latin run, a full stop and then Arabic put the stop at the wrong end. No CSS added,
      and that is a measurement: `dir` on an inline element takes `unicode-bidi: isolate` from the
      UA stylesheet.

  TWO REVIEWER PASSES, 5 FIXes + 15 NOTEs then 2 FIXes + 15 NOTEs, all closed or recorded. The
  three that changed the outcome:
  - `dir="ltr"` was moved OFF `.branch-number` onto an inner `<bdi>`: that span is a `flex: 1`
    item and `text-align: start` resolves against the ELEMENT's direction, so the plan's own
    spelling would have moved the phone number to the far edge of its box on the RTL page.
  - `docs/reference/site.md` was stale (the new prop and the new script), which would have turned
    `ci` red on its `git diff --exit-code` gate. Also `git ls-files` had to go 76 → 77: the new
    script was untracked, and the reference generator filters through `git ls-files`, so staging it
    is what made its row appear (W092).
  - `hreflang` was added to the language switch on a reviewer's suggestion and then REVERTED: it
    turned `verify` red, because `verify.sh`'s F9 assertion — merged hours earlier in #31 — greps
    `dist/404.html` for the literal string "hreflang", deliberately, since that page must promise
    no alternate. Narrowing a just-merged guard to buy a hint that no screen reader surfaces is the
    wrong trade; the attribute went instead.

  ONE VISIBLE SIDE EFFECT, STATED KNOWINGLY. `.lang-switch` declares no `font-family` and
  `[lang=ar]` in `global.css` is unqualified, so marking the switch `lang="ar"` moves its Arabic
  text from a system fallback to Tajawal on all 33 English pages. Better rendering; it also means
  Google's unicode-range-subsetted Tajawal Arabic subset is now fetched on English pages, where it
  never was, with a `display=swap` FOUT on that one chip.

  T6 IS PARTIAL, AND THE PLAN ORDERED IT THAT WAY. Its acceptance line "dist/ar/ contains no
  Latin-only label except brand names" is NOT met: `aria-label="Breadcrumb"` (product detail and
  both brand detail pages) and `aria-label="View image N"` (gallery thumbs, including the JS
  template) remain, because each needs a NEW Arabic string. Per the plan's split rule they go in a
  separate PR marked `AR: pending native review`, which is NOT pre-authorized and will be LEFT
  OPEN. That PR is the next thing after T7, and its number and exact strings will be in the final
  report.

DEVIATIONS from the plan's letter in T6, each deliberate and each in the PR body:
  - T6(e) prescribes `<span dir="ltr">` for phone numbers; shipped as `<span><bdi dir="ltr">` for
    the flex/text-align reason above.
  - T6(e) says "ASCII-only spec values"; every value is wrapped, because a spec value is
    editor-entered and `<bdi>` is inert when value and page agree.
  - `NoImageTile.astro` is not in T6's file list; the change there is a comment only (Tier 1), and
    it was necessary because the old text asserted a card's heading is always h3.
  - The Layout footer copyright and `ProductCard`'s image `alt` were not named as T6 items; both
    are the same defect class as (e) and (c) in files the plan does name.

VERIFY: 0 FAIL · 1 WARN · 67 pages. The WARN is pre-existing F5 — and one of its two dead rules is
on `.footer-heading`, the element this PR gave an `id`, so: different defect, not caused here,
still open for P5.

CODEX DID NOT REVIEW #32. Fifteen minutes after the PR opened non-draft with `ci` green, there was
no review and no inline comment. Per CLAUDE.md §5 the merge went ahead on two executor reviewer
passes plus green `ci`, and the silence is recorded, never read as approval. Running tally for
W105's dated assumption: Codex reviewed #26 and #31-at-91c8e82; it was silent on #25, on
#31-at-c9f8166 after an explicit `@codex review`, and on #32.

DEVIATIONS carried to P1.1, unchanged: T0d (dependency updates, `npm update` and `gh pr close` are
forbidden this chunk) · T1 visual watermark verdict (owner's; grep is clean) · #27 (stale duplicate
of #28, untouched).

NEXT: T7 (page-specific meta descriptions) is implemented and under executor review on branch
`chunk/p1-t7-meta`; then the pending-AR labels PR; then T8 (canon + chunk close).

NEXT-NEEDED: nothing. No decision blocks the remaining tasks.
