# Website ground truth — weekly

<!-- index: weekly independent reading of the jahjah-website project — git, clean Linux build, compiled output, live site -->

> **World-readable file. Commit subjects appear here — website strategist: confirm this is
> acceptable or request hashes-only.**

**Generated (UTC):** 2026-11-23T05:30:06Z · by `jahjah-web-truth` on the VPS work engine · **overwritten weekly**

An outside reading of `obidex/jahjah-website`, taken without touching it. This job never
pushes to that repo, never edits its files, never touches Vercel or Sanity, and never applies
a fix. **Everything below is an observation; acting on any of it is a decision for the website
strategist and the owner.**

-----

## 1. Git

The commit this run built: a fresh clone of `origin/master`, made this run and deleted next run.

| | |
|---|---|
| Commit | `48ac38e` — ci: checks scaled to the change, nightly full run, door test in parts (card #862) (#866) |
| Committed | 2026-11-22T08:58:06-08:00 |
| Branch | `master` |

### Last 10 commits

| Hash | Subject |
|---|---|
| `48ac38e` | ci: checks scaled to the change, nightly full run, door test in parts (card #862) (#866) |
| `4ea1c3a` | fix(cart): cap price quotes per calling address (card #859) (#871) |
| `6b8d8ed` | fix(auth): the password change counts toward the sign-in hold (card #858) (#870) |
| `364a844` | fix(cart): quick checkout sends a one-time key (card #857) (#867) |
| `4a1a3ef` | feat(headers): script-src policy with inline-script hashes (card #812) (#864) |
| `85d76dd` | fix(auth): a declined dealer applicant is told so at sign-in (card #856) (#865) |
| `93c115a` | fix(catalogue): listing cards merge every colour's offer (card #809) (#863) |
| `3694b9a` | chore(canon): clear audit C17 and HI02 (card #854) (#855) |
| `e00ffb8` | chore(harness): update kit 0.48.1 to 0.53.1 (#833) |
| `aca1423` | feat(auth): a signed-in customer can ask to be a dealer (card #797) (#853) |

-----

## 2. Clean build on Linux

`npm ci && npm run build` in a **fresh clone** of `origin/master` at `48ac38e`, made this run and deleted next run.
The website is developed on Windows, so this is the check that a case-sensitive filesystem
still resolves every import.

| | |
|---|---|
| Result | **clean** |
| Exit code | 0 |
| Pages built | 56 |
| Duration | 22 s |
| `dist/` size | 25M |

Notable build output:

    05:30:47 [WARN] [vite] [plugin vite-plugin-sanity-studio-chunk-warning] Some chunks are larger than 500 kB after minification. Consider:

### The website's own check: `scripts/verify.sh`

Run on this build, as the build user, with the build's three read variables (node v22.23.5). Any FAIL
fails this job.

| | |
|---|---|
| Summary | 0 FAIL · 0 WARN · 55 pages |
| Duration | 324 s |

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
| `apply.CMdM25j_.css` | 0 | 0 |
| `CartContent.DCKSdI2F.css` | 0 | 0 |
| `CheckoutContent.Bksu_FmT.css` | 0 | 0 |
| `HomeContent.CrvG-vZZ.css` | 0 | 0 |
| `Layout.BdEvPqoF.css` | 4 | 0 |
| `lib.CG2hbV0b.css` | 0 | 0 |
| `LiveProduct.DeaDdhNn.css` | 1 | 0 |
| `product-jsonld.B4JqW9l4.css` | 1 | 0 |
| `SearchContent.Lp-Bfye0.css` | 0 | 0 |
| `ServiceContent.DEh6gheP.css` | 0 | 0 |

RTL selectors in compiled order — at equal specificity, source order decides the cascade:

    Layout.BdEvPqoF.css: [dir=rtl] h1,[dir=rtl] h2,[dir=rtl] h3,[lang=ar] h1,[lang=ar] h2,[lang=ar] h3,[lang=ar] h4
    Layout.BdEvPqoF.css: [dir=rtl] .icon-flip-rtl
    LiveProduct.DeaDdhNn.css: html[dir=rtl] .breadcrumb[data-astro-cid-fnhlfdoa] li[data-astro-cid-fnhlfdoa]+li[data-astro-cid-fnhlfdoa]:before
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
