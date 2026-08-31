# Weekly dependency + image scan (trivy)

<!-- index: weekly trivy scan — 15 critical, 98 high, 161 medium, 152 low -->

**Generated (UTC):** 2026-08-31T21:00:44Z · **trivy 0.74.0** · **5 target(s)** (1 repo + 4 image(s))

Overwritten in place each week. Git history is the archive.

## Verdict

**15 critical, 98 high, 161 medium, 152 low** across all targets (447 total).

Most findings on a docker image are in the base operating-system packages of a
third-party image, not in anything this project wrote. The repo row is the one that
reflects our own dependency choices.

## By target

| Target | Critical | High | Medium | Low | Unknown |
|---|---|---|---|---|---|
| `repo: jahjah-internal (npm)` | 0 | 9 | 7 | 0 | 0 |
| `image: hello-world:latest` | 0 | 0 | 0 | 0 | 0 |
| `image: portainer/portainer-ce:lts` | 1 | 8 | 3 | 1 | 1 |
| `image: postgres:17` | 14 | 59 | 131 | 138 | 19 |
| `image: supabase/postgres:17.6.1.167` | 0 | 22 | 20 | 13 | 1 |

## Top items (critical and high only)

Showing up to 20 of 113 critical/high findings, critical first.

| Severity | Advisory | Package | Installed | Fixed in | Target |
|---|---|---|---|---|---|
| CRITICAL | `CVE-2026-56854` | `golang.org/x/crypto` | v0.54.0 | 0.55.0 | `image: portainer/portainer-ce:lts` |
| CRITICAL | `CVE-2025-68121` | `stdlib` | v1.24.6 | 1.24.13, 1.25.7, 1.26.0-rc.3 | `image: postgres:17` |
| CRITICAL | `CVE-2026-13221` | `libperl5.40` | 5.40.1-6 | none yet | `image: postgres:17` |
| CRITICAL | `CVE-2026-13221` | `perl` | 5.40.1-6 | none yet | `image: postgres:17` |
| CRITICAL | `CVE-2026-13221` | `perl-base` | 5.40.1-6 | none yet | `image: postgres:17` |
| CRITICAL | `CVE-2026-13221` | `perl-modules-5.40` | 5.40.1-6 | none yet | `image: postgres:17` |
| CRITICAL | `CVE-2026-42496` | `libperl5.40` | 5.40.1-6 | none yet | `image: postgres:17` |
| CRITICAL | `CVE-2026-42496` | `perl` | 5.40.1-6 | none yet | `image: postgres:17` |
| CRITICAL | `CVE-2026-42496` | `perl-base` | 5.40.1-6 | none yet | `image: postgres:17` |
| CRITICAL | `CVE-2026-42496` | `perl-modules-5.40` | 5.40.1-6 | none yet | `image: postgres:17` |
| CRITICAL | `CVE-2026-6653` | `libxml2` | 2.12.7+dfsg+really2.9.14-2.1+deb13u3 | none yet | `image: postgres:17` |
| CRITICAL | `CVE-2026-8376` | `libperl5.40` | 5.40.1-6 | none yet | `image: postgres:17` |
| CRITICAL | `CVE-2026-8376` | `perl` | 5.40.1-6 | none yet | `image: postgres:17` |
| CRITICAL | `CVE-2026-8376` | `perl-base` | 5.40.1-6 | none yet | `image: postgres:17` |
| CRITICAL | `CVE-2026-8376` | `perl-modules-5.40` | 5.40.1-6 | none yet | `image: postgres:17` |
| HIGH | `CVE-2026-67213` | `nanoid` | 3.3.12 | 3.3.18, 5.1.6 | `repo: jahjah-internal (npm)` |
| HIGH | `CVE-2026-67214` | `nanoid` | 3.3.12 | 3.3.16, 5.1.16 | `repo: jahjah-internal (npm)` |
| HIGH | `CVE-2026-64641` | `next` | 16.2.9 | 15.5.21, 16.2.11 | `repo: jahjah-internal (npm)` |
| HIGH | `CVE-2026-64642` | `next` | 16.2.9 | 16.2.11 | `repo: jahjah-internal (npm)` |
| HIGH | `CVE-2026-64645` | `next` | 16.2.9 | 15.5.21, 16.2.11 | `repo: jahjah-internal (npm)` |

## What was scanned

- `trivy fs --scanners vuln` over `/root/jahjah-internal` — the npm dependency tree.
- `trivy image --scanners vuln` over every image in the local docker store.
- Only the vulnerability scanner runs. The secret scanner is deliberately off here —
  secrets are covered by `SCAN-gitleaks.md`, which is built to publish a hit without
  publishing the secret.
