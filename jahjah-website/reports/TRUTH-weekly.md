# Website ground truth — weekly

<!-- index: weekly independent reading of the jahjah-website project — git, clean Linux build, compiled output, live site -->

> **World-readable file. Commit subjects appear here — website strategist: confirm this is
> acceptable or request hashes-only.**

**Generated (UTC):** 2026-09-14T05:30:04Z · by `jahjah-web-truth` on the VPS work engine · **overwritten weekly**

An outside reading of `obidex/jahjah-website`, taken without touching it. This job never
pushes to that repo, never edits its files, never touches Vercel or Sanity, and never applies
a fix. **Everything below is an observation; acting on any of it is a decision for the website
strategist and the owner.**

-----

## 1. Git

The working copy on the VPS at `/root/jahjah-website`, branch `master`. The build in §2 does **not**
use this copy — it uses a throwaway clone taken fresh from `origin/master`.

| | |
|---|---|
| HEAD | `9bdcb40` — docs: update Claude project status |
| Working tree | clean |
| Ahead of `origin/master` | 0 commit(s) |
| Behind `origin/master` | 61 commit(s) |

### Last 10 commits

| Hash | Subject |
|---|---|
| `9bdcb40` | docs: update Claude project status |
| `9a5c1d7` | feat(content): how-to-buy page (en + ar) |
| `216160e` | feat(brands): add Brand and BreadcrumbList JSON-LD on detail pages |
| `0429d33` | feat(brands): wire brand listing to Sanity + cleanup deprecated copy |
| `507b296` | feat(brands): add rich brand detail pages at /brands/[slug] |
| `b604ae7` | chore(scripts): one-shot migration of brand copy into Sanity |
| `27467c1` | feat(schema): extend brand schema with bilingual name and description fields |
| `dc950d7` | feat(brands): rewrite Brands page with confident per-brand positioning |
| `6613ba0` | feat(about): rewrite About page with confident positioning copy |
| `b6a6a11` | feat(contact): rewrite contact page with two-branch layout and catalogue-first CTA |

-----

## 2. Clean build on Linux

`npm ci && npm run build` in a **fresh clone** of `origin/master` at `96aa8a3`, made this run and deleted next run.
The website is developed on Windows, so this is the check that a case-sensitive filesystem
still resolves every import.

| | |
|---|---|
| Result | **clean** |
| Exit code | 0 |
| Pages built | 68 |
| Duration | 12 s |
| `dist/` size | 11M |

Notable build output:

    05:30:34 [WARN] [vite] [plugin vite-plugin-sanity-studio-chunk-warning] Some chunks are larger than 500 kB after minification. Consider:
    The default export of @sanity/image-url has been deprecated. Use the named export `createImageUrlBuilder` instead.

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
| `hreflang` links | 198 | 66 / 68 |
| `og:image` tags | 67 | 67 / 68 |

No `hreflang`: `client/404.html client/admin/index.html`

No `og:image`: `client/admin/index.html`

### Images (content pages, Studio excluded)

| Check | Count |
|---|---|
| `<img>` tags | 10 |
| `loading="lazy"` | 8 |
| `srcset=` | 10 |

### Real photos vs placeholder

| Check | Count |
|---|---|
| built pages: Sanity CDN image refs | 72 |
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
| `/` | 200 | ok |
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
| `/products/dcel-fridge-200` | 200 | ok |
| `/admin` | 200 | ok |
| `/og-default.jpg` | 200 | ok |
| `/robots.txt` | 200 | ok |
| `/sitemap-index.xml` | 200 | ok |
| `/images/placeholder.jpg` | 404 | **expected 200** |

| | |
|---|---|
| Homepage HTML size | 22473 bytes |
| Live `/products`: Sanity CDN image refs | 5 |
| Live `/products`: placeholder refs | 0 |

**1 path(s) did not return the expected status.**

-----

Written by `jahjah-web-truth`. Kill switch: `touch /opt/jahjah/WEB_TRUTH_OFF`. What it does
and why is in `docs/runbooks/automations.md` in `obidex/jahjah-internal`.
