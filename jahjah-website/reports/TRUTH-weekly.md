# Website ground truth — weekly

<!-- index: weekly independent reading of the jahjah-website project — git, clean Linux build, compiled output, live site -->

> **World-readable file. Commit subjects appear here — website strategist: confirm this is
> acceptable or request hashes-only.**

**Generated (UTC):** 2026-11-21T21:05:43Z · by `jahjah-web-truth` on the VPS work engine · **overwritten weekly**

An outside reading of `obidex/jahjah-website`, taken without touching it. This job never
pushes to that repo, never edits its files, never touches Vercel or Sanity, and never applies
a fix. **Everything below is an observation; acting on any of it is a decision for the website
strategist and the owner.**

-----

## 1. Git

The commit this run built: a fresh clone of `origin/master`, made this run and deleted next run.

| | |
|---|---|
| Commit | `b2fd99e` — fix(build): one retail catalogue read per build (card #815) (#822) |
| Committed | 2026-11-21T23:54:40+03:00 |
| Branch | `master` |

### Last 10 commits

| Hash | Subject |
|---|---|
| `b2fd99e` | fix(build): one retail catalogue read per build (card #815) (#822) |
| `562fb86` | fix(brands): read the brand page's brand by id, not by name (card #811) (#821) |
| `7cdfdae` | fix(admin): sign-out removes saved customer searches from the browser (card #814) (#820) |
| `40210f4` | fix(search): keep the header dropdown open on a Safari result click (card #810) (#823) |
| `134d2eb` | chore(account): drop unreachable switched-off panel and unused reset redirectTo (card #770) (#819) |
| `7bd04f7` | fix(admin): Change history keeps memberships off customer accounts (card #757) (#817) |
| `c234e33` | fix(auth): code guesses per sender and kind, code-app try limit, staff password check (card #775, 2 of 2) (#804) |
| `d822c2e` | feat(db): email-code tries per address, sender and code kind; code-app try limit (card #775, 1 of 2) (#801) |
| `b2a52c7` | docs(canon): new decisions are one file each in docs/decisions/ (inbox #716) (#800) |
| `e44ceed` | fix(orders): a retried checkout gets its first order number back after a price change (card #795) (#799) |

-----

## 2. Clean build on Linux

`npm ci && npm run build` in a **fresh clone** of `origin/master` at `b2fd99e`, made this run and deleted next run.
The website is developed on Windows, so this is the check that a case-sensitive filesystem
still resolves every import.

| | |
|---|---|
| Result | **clean** |
| Exit code | 0 |
| Pages built | 56 |
| Duration | 32 s |
| `dist/` size | 25M |

Notable build output:

    21:06:34 [WARN] [vite] [plugin vite-plugin-sanity-studio-chunk-warning] Some chunks are larger than 500 kB after minification. Consider:

### The website's own check: `scripts/verify.sh`

Run on this build, as the build user, with the build's three read variables (node v22.23.5). Any FAIL
fails this job.

| | |
|---|---|
| Summary | 0 FAIL · 0 WARN · 55 pages |
| Duration | 662 s |

-----

## 3. Compiled output

Only what `scripts/verify.sh` (section 2) does not check. It checks JSON-LD on every detail page
and both home pages, `hreflang` and `og:image` on every content page, and the compiled RTL rules
(none scope-injected), so those are not repeated here.

Read from `dist/client/`, where this build put its pages (`verify.sh` reads the same place): **55 page(s)**
(the Studio excluded) and **13 stylesheet(s)**. Counted with `grep -o | wc -l`, never `grep -c`,
which counts matching lines in minified HTML.

### Images

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

### RTL rules in `dist/client/_astro/*.css`

The minifier strips attribute-value quotes, so the compiled selector is `[dir=rtl]`, **not**
`[dir="rtl"]`. Both forms are counted: a check written with quotes finds zero and reads as a
clean bill of health.

| Stylesheet | `[dir=rtl]` (compiled form) | `[dir="rtl"]` (source form) |
|---|---|---|
| `AboutContent.Cnt_Z2kR.css` | 0 | 0 |
| `account.BAbMQ0ff.css` | 0 | 0 |
| `_...all_.uqonFUI1.css` | 0 | 0 |
| `apply.N7b8uSq6.css` | 0 | 0 |
| `CartContent.DCKSdI2F.css` | 0 | 0 |
| `CheckoutContent.Bksu_FmT.css` | 0 | 0 |
| `HomeContent.CrvG-vZZ.css` | 0 | 0 |
| `Layout.BdEvPqoF.css` | 4 | 0 |
| `lib.CG2hbV0b.css` | 0 | 0 |
| `LiveProduct.CX3-GTVU.css` | 1 | 0 |
| `product-jsonld.B4JqW9l4.css` | 1 | 0 |
| `SearchContent.Lp-Bfye0.css` | 0 | 0 |
| `ServiceContent.DEh6gheP.css` | 0 | 0 |

RTL selectors in compiled order — at equal specificity, source order decides the cascade:

    Layout.BdEvPqoF.css: [dir=rtl] h1,[dir=rtl] h2,[dir=rtl] h3,[lang=ar] h1,[lang=ar] h2,[lang=ar] h3,[lang=ar] h4
    Layout.BdEvPqoF.css: [dir=rtl] .icon-flip-rtl
    LiveProduct.CX3-GTVU.css: html[dir=rtl] .breadcrumb[data-astro-cid-fnhlfdoa] li[data-astro-cid-fnhlfdoa]+li[data-astro-cid-fnhlfdoa]:before
    product-jsonld.B4JqW9l4.css: html[dir=rtl] .breadcrumb[data-astro-cid-fnhlfdoa] li[data-astro-cid-fnhlfdoa]+li[data-astro-cid-fnhlfdoa]:before

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
