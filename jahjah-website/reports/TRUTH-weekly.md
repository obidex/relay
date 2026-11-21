# Website ground truth — weekly

<!-- index: weekly independent reading of the jahjah-website project — git, clean Linux build, compiled output, live site -->

> **World-readable file. Commit subjects appear here — website strategist: confirm this is
> acceptable or request hashes-only.**

**Generated (UTC):** 2026-11-21T16:52:03Z · by `jahjah-web-truth` on the VPS work engine · **overwritten weekly**

An outside reading of `obidex/jahjah-website`, taken without touching it. This job never
pushes to that repo, never edits its files, never touches Vercel or Sanity, and never applies
a fix. **Everything below is an observation; acting on any of it is a decision for the website
strategist and the owner.**

-----

## 1. Git

The commit this run built: a fresh clone of `origin/master`, made this run and deleted next run.

| | |
|---|---|
| Commit | `7bd04f7` — fix(admin): Change history keeps memberships off customer accounts (card #757) (#817) |
| Committed | 2026-11-21T18:44:46+03:00 |
| Branch | `master` |

### Last 10 commits

| Hash | Subject |
|---|---|
| `7bd04f7` | fix(admin): Change history keeps memberships off customer accounts (card #757) (#817) |
| `c234e33` | fix(auth): code guesses per sender and kind, code-app try limit, staff password check (card #775, 2 of 2) (#804) |
| `d822c2e` | feat(db): email-code tries per address, sender and code kind; code-app try limit (card #775, 1 of 2) (#801) |
| `b2a52c7` | docs(canon): new decisions are one file each in docs/decisions/ (inbox #716) (#800) |
| `e44ceed` | fix(orders): a retried checkout gets its first order number back after a price change (card #795) (#799) |
| `44de28f` | chore(harness): update kit 0.44.0 to 0.48.1 (#753) |
| `f7dfa9d` | fix(forms): one phone rule, refused fields marked, back re-checks (card #778) (#794) |
| `4dd6d3b` | fix(orders): sender limits, one-time key, listed showrooms (card #774, 2 of 2) (#792) |
| `911792d` | test(checks): leak scan reads every built file; probes prove tier, customer and code-step isolation (card #776) (#790) |
| `0d31987` | feat(db): order limits per sender, phone normal form, one-time order key (card #774, 1 of 2) (#789) |

-----

## 2. Clean build on Linux

`npm ci && npm run build` in a **fresh clone** of `origin/master` at `7bd04f7`, made this run and deleted next run.
The website is developed on Windows, so this is the check that a case-sensitive filesystem
still resolves every import.

| | |
|---|---|
| Result | **clean** |
| Exit code | 0 |
| Pages built | 56 |
| Duration | 29 s |
| `dist/` size | 25M |

Notable build output:

    16:53:03 [WARN] [vite] [plugin vite-plugin-sanity-studio-chunk-warning] Some chunks are larger than 500 kB after minification. Consider:

### The website's own check: `scripts/verify.sh`

Run on this build, as the build user, with the build's three read variables (node v22.23.5). Any FAIL
fails this job.

| | |
|---|---|
| Summary | 0 FAIL · 0 WARN · 55 pages |
| Duration | 660 s |

-----

## 3. Compiled output

Read from `dist/`, not from source. Counted with `grep -o | wc -l`, never `grep -c`:
`grep -c` counts matching **lines**, and Astro emits minified HTML, so two JSON-LD blocks
on one line would count as one — under-reporting exactly the pages carrying the most markup.

### JSON-LD blocks, by page type

| Page type | Pages | Blocks | Pages with none | `@type`s present |
|---|---|---|---|---|

### Head links

| Check | Total | Pages carrying it |
|---|---|---|
| `hreflang` links | 144 | 48 / 56 |
| `og:image` tags | 55 | 55 / 56 |

No `hreflang`: `client/404.html client/admin/index.html client/ar/cart/index.html client/ar/checkout/index.html client/ar/quote/index.html client/cart/index.html client/checkout/index.html client/quote/index.html`

No `og:image`: `client/admin/index.html`

### Images (content pages, Studio excluded)

| Check | Count |
|---|---|
| `<img>` tags | 0 |
| `loading="lazy"` | 0 |
| `srcset=` | 0 |

### Real photos vs placeholder

| Check | Count |
|---|---|
| built pages: Sanity CDN image refs | 0 |
| built pages: placeholder refs | 0 |

### RTL rules in `dist/_astro/*.css`

The minifier strips attribute-value quotes, so the compiled selector is `[dir=rtl]`, **not**
`[dir="rtl"]`. Both forms are counted: a check written with quotes finds zero and reads as a
clean bill of health.

| Stylesheet | `[dir=rtl]` (compiled form) | `[dir="rtl"]` (source form) |
|---|---|---|

RTL selectors in compiled order — at equal specificity, source order decides the cascade:

    (no [dir=rtl] rules in the compiled CSS)

No dead scoped RTL rules found.

-----

## 4. Live site — https://jahjah-website.vercel.app

| Path | Status | |
|---|---|---|
| `/` | 302 | **expected 200** |
| `/about` | 200 | ok |
| `/brands` | 200 | ok |
| `/products` | 200 | ok |
| `/contact` | 200 | ok |
| `/how-to-buy` | 200 | ok |
| `/ar/` | 200 | ok |
| `/ar/about` | 200 | ok |
| `/ar/brands` | 200 | ok |
| `/ar/products` | 200 | ok |
| `/ar/contact` | 200 | ok |
| `/brands/dcel` | 200 | ok |
| `/products/dcel-fridge-200` | 404 | **expected 200** |
| `/admin` | 200 | ok |
| `/og-default.jpg` | 200 | ok |
| `/robots.txt` | 200 | ok |
| `/sitemap-index.xml` | 200 | ok |
| `/images/placeholder.jpg` | 404 | **expected 200** |

| | |
|---|---|
| Homepage HTML size | 15 bytes |
| Live `/products`: Sanity CDN image refs | 0 |
| Live `/products`: placeholder refs | 0 |

**3 path(s) did not return the expected status.**

-----

Written by `jahjah-web-truth`. Kill switch: `touch /opt/jahjah/WEB_TRUTH_OFF`. What it does
and why is in `docs/runbooks/automations.md` in `obidex/jahjah-internal`.
