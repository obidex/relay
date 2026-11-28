# The agents — last 7 days

<!-- index: agents health — 6 repositories, 0 with a number GitHub would not give -->

**Generated (UTC):** 2026-11-28T05:20:14Z · kit metrics.mjs at `ac3eaf4f2eae`

Overwritten in place once a day, after `HEALTH-daily.md`. **If the time above is more than ~26 hours
old, this job has stopped and nothing here is current.** Counts only: no titles, names or numbers
of issues. How each is counted: the kit's `.harness/metrics.md`.

| Repository | Agent PRs merged | First-pass CI green | Review BLOCKs per reviewed PR | False DONEs | Stalls now (idle > 24 h) | Owner touches a week (at most) |
|---|---|---|---|---|---|---|
| obidex/jahjah-internal | 115 | 57% (21/37) | 1 (2 in 2) | 1 | 3 | 11 |
| obidex/jahjah-website | 59 | 18% (7/38) | 0.8 (5 in 6) | 0 | 16 | 5 |
| obidex/harness-kit | 28 | 96% (25/26) | 2.7 (16 in 6) | 0 | 0 | 28 |
| obidex/harness-hands | 2 | 100% (2/2) | ? (0 in 0) | 0 | 0 | 1 |
| obidex/infra | 56 | 25% (10/40) | 1.4 (11 in 8) | 2 | 3 | 86 |
| obidex/roadmap | 17 | 75% (12/16) | 2 (6 in 3) | 1 | 2 | 309 |

Last 7 days. Definitions: the kit's `.harness/metrics.md`. "?" means GitHub could not be read for it, never zero.

- obidex/jahjah-internal: partial, agent PRs (40 of 115 read)
- obidex/jahjah-internal: partial, issue events
- obidex/jahjah-internal: partial, comments
- obidex/jahjah-website: partial, agent PRs (40 of 59 read)
- obidex/jahjah-website: partial, issue events
- obidex/infra: partial, agent PRs (40 of 56 read)

## Repeated CI runs (kit ci-repeats.mjs at `1b332805d5dc`)

From the job records the VPS's minutes count keeps (the private ones among the repositories above, every
job attempt once, on any runner). Numbers only. How each is counted: the kit's `.harness/metrics.md` §4.

| Repository | Avoidable runner-hours a week | Ready to green (median; p90; changes) | Check runs per merged change | Merged a day | Runner-hours: same · retry · bot · base · main · change · other | Cancelled hours (flag) | First pass (green of ready; failures by cause: product · unreliable test · infrastructure · old finding) |
|---|---|---|---|---|---|---|---|
| obidex/harness-hands | 0 (58.8% of CI) | ? min; ? min; 0 | 1 | 0.1 | 0 · 0 · 0 · 0 · 0 · 0 · 0.2 | 0 | - |
| obidex/infra | 1.9 (34.8% of CI) | 14.9 min; 29.1 min; 2 | 1.5 | 0.6 | 1.9 · 0 · 0 · 0 · 0 · 3.6 · 0.8 | 0.4 | 100% (2/2); 0 · 0 · 0 · 0 |
| obidex/jahjah-internal | 3.9 (19.5% of CI) | 20.6 min; 49 min; 25 | 2 | 4.4 | 3.9 · 0 · 0 · 5.5 · 0.5 · 10.1 · 4.6 | 0.4 | 100% (26/26); 0 · 0 · 0 · 0 |
| obidex/jahjah-website | 1.2 (23.9% of CI) | 50.4 min; 50.4 min; 1 | 1 | 1.1 | 1.2 · 0 · 0 · 1.6 · 0.2 · 2.2 · 2.2 | 0.1 | 100% (3/3); 0 · 0 · 0 · 0 |
| obidex/roadmap | 0 (25.9% of CI) | ? min; ? min; 0 | 1 | 0.3 | 0 · 0 · 0 · 0 · 0 · 0.1 · 1.8 | 0 | - |

Last 7 days, from records kept since 2026-11-27T08:25:02Z. Avoidable = same + bot. First pass: ready changes green the first time; how each cause is told: the kit's metrics.md §5. "?" means not measured, never zero.

