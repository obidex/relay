=== REPORT: P1-launch-blockers · progress ===
HEAD: 3c30b37 | tree: clean | branch: chunk/p1-t6-a11y (T6 in flight) | git ls-files: 76

PRs:
  #31 3c30b37 MERGED — T5: 404 page is noindex, emits no hreflang and no canonical, and its
      language toggle points at a route that exists. Closes ROADMAP F9.

CI: PR #31 final commit c9f8166 green (run 33843765568, 7m54s) · post-merge master green
    (run 33845094591, completed 06:45:01Z). An earlier run on 91c8e82 shows CANCELLED — superseded
    by the new commit, not a failure.
PROD: master deployment READY — verified by probing the live site rather than the dashboard.

LIVE PROBES (per-path curl now works — the allowlist repair in #29 closed the gap the last
session reported):

| path | status |
|---|---|
| `/` | 200 |
| `/ar/` | 200 |
| `/products/` | 200 |
| `/ar/products/` | 200 |
| `/about/` | 200 |
| `/brands/` | 200 |
| `/contact/` | 200 |
| `/how-to-buy/` | 200 |
| `/admin/` | 200 |
| `/nonexistent-path/` | 404 |
| `/ar/nonexistent-xyz/` | 404 |

Live `/ar/nonexistent-xyz/` body, fetched from production after the merge: 0 links to `/ar/404`,
0 `hreflang`, 0 `rel="canonical"`, `noindex,nofollow` present, `<html lang="en" dir="ltr">`,
0 assignments to `documentElement.lang`/`.dir`, and both language blocks carrying their own
`lang`/`dir`. F9 is closed on the live site, not merely in the build.

DONE — T5-close.

  The session before this one pushed T5, went green, received the Codex review, and then ended
  without merging or reporting. The branch was found intact and matching its remote, with no
  uncommitted work and no stashes, so nothing was lost and nothing had to be folded in.

  CODEX ON #31 — two findings, both labelled P2, so both below THE BAR. One was fixed anyway
  and one answered, both in the PR thread:

  1. *"Keep the English layout chrome marked as English"* — **FIXED in c9f8166.** The pushed
     commit flipped `document.documentElement.lang` to `ar` and `dir` to `rtl` on `/ar/*` paths.
     The header, nav, footer, language toggle and the English block are English on every 404
     whatever the path — one HTML file answers all of them — so that flip handed all of it Arabic
     language semantics and an RTL mirror. Inside a chunk whose stated job is accessibility, that
     ships a net accessibility regression; it is the same defect the branch had already caught one
     level down, where the English block inherited RTL. The document root now stays
     `lang="en" dir="ltr"` and each block declares its own `lang`/`dir` — the correct HTML for a
     mixed-language document, and what a screen reader actually reads. Nothing was lost: the
     Arabic half still announces as Arabic, still sets in Tajawal (`[lang=ar]` compiles as an
     unqualified attribute selector, so it matches the `<section>` directly) and still lays out RTL.
  2. *"Derive the 404 toggle from the visitor URL"* — **ANSWERED, not changed.** Deriving it would
     make the page less coherent: with the root left English, an Arabic 404 is an English-chrome
     page whose nav links already go to `/`, `/about`, `/products`, so a toggle rewritten to read
     "English → /" would be a switch-to-English affordance sitting in chrome that is already
     English. What ships — العربية → `/ar/` — is a true statement about where the link goes. The
     honest limit is stated in the PR rather than glossed: `html.ar-primary` swaps CSS `order`
     only, so DOM order is unchanged and an Arabic visitor still traverses the English header and
     block before reaching an Arabic CTA. Pre-existing on master, outside T5's spec.

  EXECUTOR REVIEWER — four FIXes, all applied in c9f8166:
  - The `verify.sh` root-flip tripwire is anchored on the ASSIGNMENT,
    `documentElement\.(lang|dir)[[:space:]]*=`, not the bare identifier. An `is:inline` script is
    neither bundled nor minified, so its own comments ship inside the file being grepped, and the
    unanchored form would have turned CI red for DOCUMENTING the rule. Measured three ways: 0 on a
    file that only mentions the identifiers (the unanchored form scores 2 there), 2 on the flip
    re-introduced into the built page, 0 on current dist.
  - The `noindex` meta goes back to `noindex,nofollow`. Normalising it to the spaced form was
    unrelated to F9 (CLAUDE.md §6) and falsified the comment PR #30 left at the `/admin`
    assertion, which states that Layout emits it unspaced. Both assertions tolerate either
    spelling, so nothing needed the change.
  - ROADMAP F9 is closed in this PR with its cause corrected in place, rather than deferred to
    chunk end — otherwise the register would have described code that does not exist for the
    length of the chunk.
  - The PR body was rewritten: it still claimed the root flip as a feature and justified
    `.block-en`'s attributes by it.

  What actually closed F9, restated because the register had it wrong: the `/ar/404/` the link
  checker saw was the language **toggle's own href** — root-relative, which is all that check can
  match. An hreflang alternate is an absolute URL and was never visible to it. The fix is Layout's
  `oppositeUrl` fallback. Suppressing the canonical and the three alternates is correct on its own
  merits and shipped in the same PR, but it is not what was failing.

  `dist/404.html` also sat outside every other check in `verify.sh`, because the page-count walk
  only visits `<dir>/index.html`. Four positive assertions now run against it.

VERIFY: 0 FAIL · 1 WARN · 67 pages. The WARN is pre-existing F5 (two scope-injected `[dir=rtl]`
dead rules, P5). The F9 "dead hreflang alternate" WARN is gone.

REVIEWER OF RECORD — CODEX DID NOT RE-REVIEW, AND THAT IS RECORDED, NOT READ AS APPROVAL.
Codex reviewed 91c8e82 at 2026-09-03T20:03:16Z with two P2 inline comments. After c9f8166 was
pushed and an explicit `@codex review` comment posted, it stayed silent for ~17 minutes: no review
on the new commit, no new inline comments. Per CLAUDE.md §5 the merge went ahead on the executor
reviewer plus green `ci`, and the silence is reported. This is the second data point on W105's
dated assumption: Codex reviewed #26 and #31-at-91c8e82, and was silent on #25 and on
#31-at-c9f8166 including after an explicit request.

DEVIATIONS (carried to P1.1, unchanged from the last report):
  - T0d — dependency updates and closing the six Dependabot PRs. This session is forbidden
    `npm update` and `gh pr close` by its own chunk plan.
  - T1 visual half — the watermark verdict on the DCEL washer images is the owner's; grep is clean.
  - #27 — the stale duplicate of #28. Untouched, as instructed.
  - The per-path probe table, previously a deviation, is CLOSED: the table above is real.

NEXT: T6 (accessibility) is implemented and under executor review on branch
`chunk/p1-t6-a11y`; then T7 (page-specific meta descriptions), then T8 (canon + chunk close).

NEXT-NEEDED: nothing. No decision blocks the remaining tasks.
