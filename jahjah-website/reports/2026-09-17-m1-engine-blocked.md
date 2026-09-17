M1-engine stopped at T1 on one of its own STOP conditions: the edit hook, as specified, blocks a clean file. `npx tsc --noEmit -p tsconfig.json` already fails on `master` with 5 errors in two files this chunk may not touch, so every `.ts` edit exits 2 and the new CI step could never go green. The hooks, the `typescript` devDependency and the lockfile's `libc` fields are written and tested on a local branch (not pushed), and the T0 labels are done. One ruling unblocks the chunk: how the project gets to a clean type-check (option A below is recommended). Nothing was merged and `master` is unchanged.

```
=== REPORT: M1-engine · blocked ===
HEAD: a884e8e (master, unchanged) | tree: clean | branch: chunk/m1-t1-hooks @ faa576c (local only)
PRs: none
CI: not run (nothing pushed) · PROD: unchanged | live probes: not run (no deploy)
DONE: T0 · #114 chunk:proposed -> chunk:running; 14 labels created, none refused
DONE: T1 partial, local · typescript ^5.9.3 devDependency; 29 libc fields npm stripped restored + 18 taken
      from the registry (47 total; the 2 arm-gnueabihf builds publish none) · post-edit.sh, pre-bash.sh
      (parser in pre-bash.mjs) · bash -n 0/0 · pre-bash 58/58 cases (rm -rf /etc/x -> 2, rm -rf dist -> 0)
      · post-edit .mjs clean 0 / broken 2 · npm ci 0 · build 68 · verify 0 FAIL 0 WARN, 67 pages
DEVIATIONS: tsconfig.json already exists (extends astro/tsconfigs/strict, include **/*), so the plan's
      "create the minimal one" branch did not apply
FINDINGS/BLOCKERS:
 1. tsc exits 2 on master: astro.config.mjs:19 TS2741 (image.endpoint has no `route`), :54 TS7006
    (`seg` implicit any); src/sanity/schemaTypes/product.ts:167, :183, :299 TS2347 (`client.fetch<number>`
    on an `any` client). 167 and 183 sit inside the W013 slug validator.
 2. Hence post-edit exits 2 on a clean src/ probe file (STOP: "a hook that blocks a clean file"), and
    the CI step cannot pass. Excluding product.ts alone does not help: sanity.config.ts imports it.
 3. A fresh checkout also lacks .astro/types.d.ts: 3 more errors in src/middleware.ts (`astro:middleware`
    unresolved). `npx astro sync` (no secrets needed) clears them, and Build regenerates the file too.
 4. For T5: the `claude remote-control` subcommand lists no --settings/--model/--effort, while
    `claude --remote-control "<name>"` takes all three. T5 will test the plan's spelling first.
 5. Timers recorded by name; jahjah-web-dispatch.timer is the one T4's install.sh disables.
CANON: none
NEXT-NEEDED: one ruling (below)
=== END ===
```

**Decision needed.** Pick one, record it on this issue, then the owner types `resume M1`.

- **A (recommended): authorize `tsconfig.json` in T1.** Add `"exclude": ["dist", "astro.config.mjs", "sanity.config.ts", "sanity.cli.ts", "src/sanity"]`, and put the CI step after Build (or after `npx astro sync`). Measured on a fresh checkout: exit 0, with 39 project files checked (all of `src/lib`, `src/middleware.ts`, `src/pages/api`, `src/utils`). A new F-row carries the 5 excluded errors to a card that names `astro.config.mjs` and `product.ts`, with a W013 exception.
- **B: authorize fixing the 5 errors in T1.** That means `astro.config.mjs` (`route: '/_image'`, a typed `seg`) and `product.ts` (three typed fetches, two of them inside the slug validator, so an explicit W013 exception). Coverage is full, but it puts Tier-3 edits to the build config and the Studio schema inside an engine chunk.
- **C: ship the hooks without the CI step and keep F55 open.** post-edit would report only errors in the edited file: a clean file exits 0, and breakage in other files goes unseen.

Why A: every server-side `.ts` file (W086) gets the hook and CI coverage now, no runtime file changes, and the two Tier-3 fixes go to a card that can review them properly.

**Left untouched:** `master`, `.claude/**`, `.github/**`, `tsconfig.json`, `astro.config.mjs`, `product.ts`. No PR was opened and no branch was pushed. Caps: about 20 min of the 6 h session cap used; T1 retries 0 of 2.
