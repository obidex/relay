**In plain words:** The Vercel adapter is added in PR 67. The build is unchanged: the same 68 pages, byte for byte, and no server function. So the site stays fully static on the free plan. One thing cannot be checked from the build alone, and it is why T2 stops here. The Studio's deep links (`/admin/structure`) have always worked through three rewrites in `vercel.json`. With the adapter, Vercel serves a routing file the adapter writes, and those rewrites are not in it. The build cannot show whether Vercel still applies them, so the preview has to. Since the last report, preview protection has been switched off, so anyone can now open the preview. **My own probes of it say the rewrites do survive**: all three `/admin` paths serve the Studio shell, and every other path matches production. The verdict is still the strategist's, not mine. **T2 is awaiting strategist preview verification. The PR does not merge until a verdict line is pasted into this session.**

=== REPORT: P2b-1-astro7-adapter · progress (T2 awaiting strategist preview verification) ===
HEAD: master 1bc78cf | tree: clean | branch: chunk/p2b1-t2-adapter at 483306a
PRs: #62 1bc78cf merged (T1) · #67 483306a open — T2 Vercel adapter, **not merged, awaiting verdict**
CI: #67 `ci` green — run 34503933247 (1m25s, tier3-guard passed) on `483306a` · https://github.com/obidex/jahjah-website/actions/runs/34503933247 · Vercel preview check pass
PROD: deployment 6376393238 (`1bc78cf`) READY — unchanged by T2, which is not merged
DONE:
- T2: `@astrojs/vercel` 11.0.10 added (the one new dependency the plan names; peer `astro ^7.0.0`); `astro.config.mjs` gains the import and `adapter: vercel()` — no options, output stays static. 29 `libc` restored again (W123).
- (1) `.vercel/output/` = `config.json` + `static/` (exactly `dist/`, 68 HTML) + an empty `server/`; **no `functions/` directory — no serverless function.**
- (2) `config.json` routes: `{"handle":"filesystem"}` → `^/_astro/(.*)$` cache header → catch-all `^/.*$` → `/404.html` status 404. **The three `vercel.json` `/admin` rewrites are NOT in it**; the adapter passes `rewrites: []`. Whether Vercel applies `vercel.json` on top is the preview question.
- (3) `dist/` **68/68 HTML byte-identical** to T1's build. verify 0 FAIL · 0 WARN · 67. Reference: Adapter `none` → `@astrojs/vercel`.
- (4) PR opened, and **STOPPED**.
FINDINGS/BLOCKERS:
- `npm audit` 17 → 20 (high 9 → 12, critical 0): one chain, `@astrojs/vercel` → `@vercel/routing-utils` → `path-to-regexp` (ReDoS class). Build time only, over our own route patterns; nothing is served.
- `.vercel/` is not gitignored. The build output was deleted before commit. A `.gitignore` rule is for T5 or a later plan to name.
- Executor reviewer on T2: `REVIEW: 6 ISSUES` — **0 BLOCK · 0 FIX · 6 NOTEs**, all answered in the PR. The one that matters: **my reading that the deep links would 404 is only one of two.** `@vercel/routing-utils` places `vercel.json` rewrites after the filesystem check and ahead of the builder's catch-all, so if Vercel merges `vercel.json` with the adapter's `config.json`, the rewrites win. Only the preview can say which. Also: the audit advisory is GHSA-9wv6-86v2-598j, and npm's only fix is a major downgrade to adapter 8.0.4, which Astro 7 cannot use. And `verify.sh` does not check `.vercel/output/`, which is for a later chunk.
Codex (#67, from `createdAt` 16:44:22Z): 👀 at 16:44:28Z (looking, not a verdict), then **👍 at 16:49:37Z (5m15s)**. That is the "reviewed, nothing found" verdict. Four surfaces read: reviews 0 · inline 0 · Codex issue comments 0 · reactions 👍.
Preview: deployment 6376618328, `https://jahjah-website-a8it489xe-gigis-projects-035f890d.vercel.app`. Vercel's build of the adapter output succeeded (status success at 16:44:27Z).
- **Protection changed since the blocked report:** `ssoProtection.enabled: false` (read-only Vercel MCP read of the project; password and trusted-IP protection off too). #60's and #62's previews now answer 200 as well. I did not make the change; it is a dashboard setting, and only the owner can change it. W090's "unprotected previews" is true again.
- **The executor's own unauthenticated probes of this preview, before any verdict** (production today in brackets):
  - `/admin` 200 · `/admin/` 200 · `/admin/structure` 200 · `/admin/structure/product` 200, **each 1071 bytes and byte-identical to the built Studio shell**, with the `…index_0_lang.Be7W1HaD.js` script tag and `noindex, nofollow` [200, 1071 B]. **The `vercel.json` rewrites survive the adapter.**
  - `/` 200 · `/ar/` 200 · `/products/` 200 · `/ar` 200 · `/products` 200 (no redirect) [all 200].
  - `/404` 404 · `/no-such-page/` 404, both serving the site's own 404 page [404].
  - `/sitemap-0.xml` 200, byte-identical to the build · `/sitemap-index.xml` 200 · `/robots.txt` 200 [200].
  - `?dpl=` on asset URLs: **0**, so skew protection is off.
- **One byte difference, explained:** the preview's `/` is 22636 bytes against production's 22473. Vercel injects its preview feedback-toolbar script (`vercel.live/_next-live/feedback/feedback.js`) after `</body>` on preview deployments. Production's `/` is byte-identical to the built `index.html`. This is not our output.
VERIFY THIS, on the preview, signed in (production today in brackets):
- `/admin`, `/admin/structure`, `/admin/structure/product` → 200 and the Studio shell, which has a `<script type="module" src="/_astro/_...all_.astro_astro_type_script_index_0_lang.…js">` tag and `noindex, nofollow` [200, 1071 bytes]. **A 404 page on either deep link means T2 FAILED.**
- `/`, `/ar/`, `/products/` → 200 [200].
- `/ar` and `/products`, with no trailing slash → 200 [200]. These cannot be tested locally.
- `/404` and a miss such as `/no-such-page/` → 404 [404].
- `/sitemap-0.xml`, `/sitemap-index.xml`, `/robots.txt` → 200 [200]. The sitemap is unchanged: 66 URLs, no `/admin`.
- No `?dpl=` in any page's asset URLs. That would mean skew protection is on, which the local build cannot see.
CANON: none yet — T5.
NEXT-NEEDED: **the verdict line, pasted into this session: "T2 VERIFIED — merge" or "T2 FAILED — revert".** On "merge": merge with `--subject`, watch the post-merge run, confirm production READY and live `/admin/structure` 200, continue to T3. On "revert": close #67 unmerged, record the W-entry "adapter deferred to the on-demand route; vercel.json rewrites do not survive the adapter", continue to T3.
=== END ===
