# W1 — website project onboarded read-only, `jahjah-web-truth` built

<!-- index: onboarded jahjah-website read-only, first clean Linux build (68 pages), built the weekly truth job, F7 portainer no-op -->

**When (UTC):** 2026-09-01 · **Box:** VPS work engine · **Session:** infra, read-only on the website project

Onboards a **second** project, `obidex/jahjah-website`, as an object of observation only. Nothing in
this session pushed to that repo, modified its files, or touched Vercel or Sanity. Every problem
found is written down below and left alone.

-----

## 1. F7 — Portainer: no update exists, container NOT recreated

`docker pull portainer/portainer-ce:lts` returned **`Status: Image is up to date`**. The running
container is already on that exact image and the binary reports **2.45.0**:

| | |
|---|---|
| `:lts` tag resolves to | `sha256:511f3f06…` |
| Running container `.Image` | `sha256:511f3f06…` — identical |
| Version | 2.45.0 |
| Container created | 2026-08-31 17:15 UTC (already recreated on this image) |

**I did not recreate the container.** The approval's own verification step — *"verify version newer"*
— cannot be satisfied, because there is no newer version. Recreating on an identical digest achieves
nothing, costs a short Portainer outage, and puts the port bindings through a hand-retyped
`docker run`. Bindings are unchanged and still `127.0.0.1:9443` + `10.66.66.1:9443` only. Say the word
if the recreate is wanted anyway as a drill; the flags would be read back from the live container
rather than retyped.

**Trivy count for it — unchanged, and already measured against this exact digest** by the weekly scan
at 2026-08-31T23:13:55Z: **1 critical, 8 high, 3 medium, 1 low, 1 unknown.**

**The finding that matters:** the critical is `CVE-2026-56854` in `golang.org/x/crypto v0.54.0`, fixed
upstream in 0.55.0. **It is present in the newest LTS image.** Pulling cannot clear it — only a
Portainer rebuild against a patched Go toolchain will. There is nothing to do here but wait and
re-check; the weekly scan already does that.

-----

## 2. The website project, onboarded read-only

Cloned to `/root/jahjah-website`, branch `master`, HEAD `9bdcb40` — **the repo has been dormant for
3.5 months** (last push 2026-05-18). Working tree clean, level with `origin/master`.

The credentials file was placed by the owner over `scp` and locked to mode `600`. The three variables
the build needs are present and non-empty, **verified by name only** — no value was read, printed or
logged.

-----

## 3. First-ever clean Linux build — PASSED

| | |
|---|---|
| `npm ci` | exit 0, 48 s, 1158 packages |
| `npm run build` | **exit 0** |
| Pages built | **68** (matches the ~68 EN+AR expectation exactly) |
| Duration | 37 s wall (35.4 s reported by Astro) |
| `dist/` size | 9.8 MB |

**No case-sensitivity breakage.** This was the main risk — the project is developed on Windows and had
never been built on a case-sensitive filesystem. Every import resolves.

-----

## 4. FINDINGS for the website strategist

**None of these were fixed. None should be read as a request.** They are observations from outside,
listed newest-risk first, for that project's own tiering to decide on.

### 4.1 `/images/placeholder.jpg` does not exist — 126 image tags point at a 404

The file is absent from `public/images/` in the repo, absent from `dist/`, and **returns 404 in
production**. `src/utils/sanity.js` falls back to it for every product without a photo, so 126 `<img>`
tags across the built site (21 on the live `/products` page alone) request a file that is not there.

An `onerror` handler hides the broken image and reveals a text fallback, so it degrades quietly rather
than breaking visibly — which is why it has survived. Every product card still fires a failed request.

### 4.2 Product JSON-LD fires on 1 product out of 22

Their own notes name the cause: `Product` schema requires an image, and only one product
(`dcel-washer-front-7kg`) has photos in Sanity. So 21 of 22 products ship with `BreadcrumbList` only
and no `Product` markup at all. **This is a content gap, not a code defect** — it closes as photos are
uploaded, and the weekly report now tracks the number so the progress is visible.

Compiled counts, EN and AR together:

| Page type | Pages | JSON-LD blocks | With none | `@type`s present |
|---|---|---|---|---|
| home | 2 | 2 | 0 | Organization, Place, PostalAddress, Country |
| product detail | 44 | 46 | 0 | BreadcrumbList, ListItem, Product, Brand |
| brand detail | 10 | 20 | 0 | Brand, BreadcrumbList, ListItem |
| listing pages | 4 | 0 | 4 | — |
| static pages | 7 | 0 | 7 | — |

Listing pages, About, Contact, How-to-buy and 404 carry no structured data. That may well be
deliberate; it is reported, not judged.

### 4.3 A dead RTL rule — Arabic product titles keep Latin letter-spacing

`src/components/pages/ProductDetail.astro:427` writes `[dir="rtl"] .product-name { letter-spacing: 0 }`.
Astro attaches the component's own scope id to a **leading attribute selector**, so it compiles to:

    [data-astro-cid-bnnekw4u][dir=rtl] .product-name[data-astro-cid-bnnekw4u]

That needs one element carrying **both** the scope id and `dir=rtl`. `dir` lives on `<html>`, which
carries the **layout's** id (`data-astro-cid-sckkx6r4`), not ProductDetail's. **No element matches, so
the rule never applies** and Arabic product titles keep the `-0.03em` tracking the rule exists to
cancel.

**This is exactly the pitfall their own CLAUDE.md §13 documents** — "chain dir on the `html` element
directly: write `html[dir="rtl"] .x`". The fix would be a one-word change; the same file gets it right
three lines earlier for `.breadcrumb`. `Layout.astro:503` has the identical shape for `.footer-heading`
and works only **by luck**, because `<html>` happens to carry the layout's id.

### 4.4 No lazy loading and no responsive images anywhere

Across 192 `<img>` tags on the 67 content pages: **`loading="lazy"` — 0. `srcset` — 0.** Neither
appears in the source or the compiled output. Every image on every page loads eagerly at a single
resolution. On a catalogue site whose audience is largely on mobile, this is the cheapest performance
win available, and it gets more valuable as real photos replace placeholders.

### 4.5 29 npm vulnerabilities in the dependency tree

`npm ci` reports **29 (1 critical, 16 high, 10 moderate, 2 low)** on a lockfile untouched for 3.5
months. Not triaged here — the tree is largely Sanity Studio, which ships a lot of transitive
surface — but it is 3.5 months stale and worth one deliberate look.

### 4.6 Two smaller build notes

- `@sanity/image-url`'s default export is deprecated; `src/utils/sanity.js:2` uses it. The named
  export `createImageUrlBuilder` is the replacement.
- The Sanity Studio chunk exceeds 500 kB after minification. Expected for an embedded Studio, and it
  only affects `/admin`, which staff use and customers do not.

### 4.7 Live site — everything reachable

All 14 probed content paths return 200, EN and AR. **`/admin` returns 200.** `/404` correctly returns
a 404 status. `robots.txt`, `sitemap-index.xml` and `og-default.jpg` all serve. Homepage HTML is
**19,162 bytes**. The live `/products` page shows **1 real Sanity image against 21 placeholders**,
matching the local build exactly — so the deployment is current with `master`.

`hreflang` (en / ar / x-default) and `og:image` are present on **67 of 68 pages**; the only page
without them is the Studio mount, which is correct.

-----

## 5. `jahjah-web-truth` — the sixth automation, and the first about another project

Everything above is now measured every week without anyone asking.

| | |
|---|---|
| **Schedule** | Mondays **05:30 UTC**, plus `systemctl start jahjah-web-truth.service` on demand |
| **Report** | `raw.githubusercontent.com/obidex/relay/main/jahjah-website/reports/TRUTH-weekly.md` |
| **Kill switch** | `touch /opt/jahjah/WEB_TRUTH_OFF` |
| **Armed** | `list-timers` shows a real NEXT: **Mon 2026-09-07 05:30 UTC** |
| **Proven** | two runs triggered **through systemd**, both `Result=success`, 61 s and 58 s |

It obeys the three owner laws: caps and timeouts at every step with a `TimeoutStartSec=2700` ceiling;
a 3-strike self-disable that publishes an `ALERT` to the relay; a `README.md`, a plain-language
`Description=` naming the kill switch, and a registry row.

**Its hard limit is read-only.** It builds in a throwaway clone it makes and deletes each run — never
in a working copy — so a build can never disturb someone's work. It keeps its **own** credentials
file holding only the read-only variables, so a job that only reads carries nothing that can write.
The single write it performs near that project is `git fetch` in the working copy, which touches
remote-tracking refs under `.git/` only and cannot alter a file, a branch or the checked-out tree; it
is there so "ahead/behind origin/master" is true rather than stale.

### One disclosure decision for the website strategist

The report's header carries this line, verbatim, on the file itself:

> **World-readable file. Commit subjects appear here — website strategist: confirm this is acceptable
> or request hashes-only.**

Commit **subjects** (e.g. "feat(brands): add rich brand detail pages") appear in the last-10-commits
table. If that is not acceptable, the change is one word in the job and the column disappears. Nothing
else about that project is published: no environment values, no tokens, no unpublished content — the
build uses Sanity's `published` perspective, so drafts are never fetched — and nothing about pricing.

### Two traps this job is built around, both of which reported a false all-clear first

1. **`grep -c` counts matching LINES, not matches.** Astro emits minified HTML, so the first pass
   reported **one** JSON-LD block on every brand detail page and on the one product page carrying
   `Product` markup — each of which actually has **two**. It under-reports exactly the richest pages,
   so the number looks plausible and reads low. Every count in the job uses `grep -o | wc -l`.
2. **The CSS minifier strips attribute-value quotes.** The compiled selector is `[dir=rtl]`, never
   `[dir="rtl"]`. A check spelling the source form returns **zero across every stylesheet** and reads
   as a clean bill of health. The report counts **both** forms side by side so the gap is visible —
   and it was this that surfaced finding 4.3.

Trap 1 is now recorded in `docs/runbooks/automations.md` §5, because it will bite any future job that
counts occurrences in compiled output.

-----

## 6. What changed in `jahjah-internal`

Commit **`8d980d3`**, pushed to `main`.

| File | |
|---|---|
| `infra/vps/web-truth/web-truth.sh` | new — the job |
| `infra/vps/web-truth/README.md` | new — its plain-language note (law 3) |
| `infra/vps/systemd/jahjah-web-truth.{service,timer}` | new — `systemd-analyze verify` clean |
| `infra/vps/lib/jahjah-common.sh` | one line — classify `TRUTH-*` as a standing file in the generated index |
| `docs/runbooks/automations.md` | the registry row, the standing-file and retention-exemption lists, the relay-lock writer count, and the new §5 trap |
| `infra/vps/README.md` | drift check and runtime-state table cover the new job |

Repo and box verified in sync by the drift check. Nothing in `/root/jahjah-website` was modified.

-----

## 7. CI

`8d980d3` on `main`: **all four jobs green** — secret scan (gitleaks), static analysis (semgrep),
lint/type-check/unit tests, and the SQL suites.
<https://github.com/obidex/jahjah-internal/actions/runs/33454041050>
