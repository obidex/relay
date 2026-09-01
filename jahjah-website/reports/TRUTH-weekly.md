# Website ground truth — weekly

<!-- index: weekly independent reading of the jahjah-website project — git, clean Linux build, compiled output, live site -->

> **World-readable file. Commit subjects appear here — website strategist: confirm this is
> acceptable or request hashes-only.**

**Generated (UTC):** 2026-09-01T00:09:57Z · by `jahjah-web-truth` on the VPS work engine · **overwritten weekly**

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
| Behind `origin/master` | 0 commit(s) |

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

`npm ci && npm run build` in a **fresh clone** of `origin/master` at `9bdcb40`, made this run and deleted next run.
The website is developed on Windows, so this is the check that a case-sensitive filesystem
still resolves every import.

| | |
|---|---|
| Result | **clean** |
| Exit code | 0 |
| Pages built | 68 |
| Duration | 35 s |
| `dist/` size | 9.8M |

Notable build output:

    00:10:51 [WARN] [vite] [plugin vite-plugin-sanity-studio-chunk-warning] Some chunks are larger than 500 kB after minification. Consider:
    The default export of @sanity/image-url has been deprecated. Use the named export `createImageUrlBuilder` instead.

-----

## 3. Compiled output

Read from `dist/`, not from source. Counted with `grep -o | wc -l`, never `grep -c`:
`grep -c` counts matching **lines**, and Astro emits minified HTML, so two JSON-LD blocks
on one line would count as one — under-reporting exactly the pages carrying the most markup.

### JSON-LD blocks, by page type

| Page type | Pages | Blocks | Pages with none | `@type`s present |
|---|---|---|---|---|
| home (EN+AR) | 2 | 2 | 0 | Country,Organization Place,PostalAddress |
| product detail | 44 | 46 | 0 | Brand,BreadcrumbList ListItem,Product |
| brand detail | 10 | 20 | 0 | Brand,BreadcrumbList ListItem |
| listing pages | 4 | 0 | 4 | — |
| static pages | 7 | 0 | 7 | — |
| studio mount | 1 | 0 | 1 | — |

### Head links

| Check | Total | Pages carrying it |
|---|---|---|
| `hreflang` links | 201 | 67 / 68 |
| `og:image` tags | 67 | 67 / 68 |

No `hreflang`: `admin/index.html `

No `og:image`: `admin/index.html `

### Images (content pages, Studio excluded)

| Check | Count |
|---|---|
| `<img>` tags | 192 |
| `loading="lazy"` | 0 |
| `srcset=` | 0 |

### Real photos vs placeholder

| Check | Count |
|---|---|
| built pages: Sanity CDN image refs | 36 |
| built pages: placeholder refs | 126 |

### RTL rules in `dist/_astro/*.css`

The minifier strips attribute-value quotes, so the compiled selector is `[dir=rtl]`, **not**
`[dir="rtl"]`. Both forms are counted: a check written with quotes finds zero and reads as a
clean bill of health.

| Stylesheet | `[dir=rtl]` (compiled form) | `[dir="rtl"]` (source form) |
|---|---|---|
| `Layout.B2KQ5eIl.css` | 7 | 0 |
| `ProductDetail.B5owrAoa.css` | 2 | 0 |

RTL selectors in compiled order — at equal specificity, source order decides the cascade:

    Layout.B2KQ5eIl.css: [dir=rtl] h1,[dir=rtl] h2,[dir=rtl] h3
    Layout.B2KQ5eIl.css: [dir=rtl] .icon-flip-rtl
    Layout.B2KQ5eIl.css: html[dir=rtl] .mobile-panel[data-astro-cid-sckkx6r4]
    Layout.B2KQ5eIl.css: html.menu-open .mobile-panel[data-astro-cid-sckkx6r4],html[dir=rtl].menu-open .mobile-panel[data-astro-cid-sckkx6r4]
    Layout.B2KQ5eIl.css: [data-astro-cid-sckkx6r4][dir=rtl] .footer-heading[data-astro-cid-sckkx6r4]
    ProductDetail.B5owrAoa.css: html[dir=rtl] .breadcrumb[data-astro-cid-bnnekw4u] li[data-astro-cid-bnnekw4u]+li[data-astro-cid-bnnekw4u]:before
    ProductDetail.B5owrAoa.css: [data-astro-cid-bnnekw4u][dir=rtl] .product-name[data-astro-cid-bnnekw4u]

**Dead RTL rules found.** `dir` lives on `<html>`, which carries the layout's scope id.
Astro attaches a component's own scope id to a leading attribute selector, so an RTL rule
written as `[dir="rtl"] .x` inside any other component compiles to a selector that requires
one element to hold both ids — which no element does:

- `[data-astro-cid-bnnekw4u][dir=rtl] …` in `ProductDetail.B5owrAoa.css` — `<html>` carries `data-astro-cid-sckkx6r4`, so this selector can never match.

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
| Homepage HTML size | 19162 bytes |
| Live `/products`: Sanity CDN image refs | 1 |
| Live `/products`: placeholder refs | 21 |

**1 path(s) did not return the expected status.**

-----

Written by `jahjah-web-truth`. Kill switch: `touch /opt/jahjah/WEB_TRUTH_OFF`. What it does
and why is in `docs/runbooks/automations.md` in `obidex/jahjah-internal`.
