# Weekly dependency + image scan (trivy)

<!-- index: weekly trivy scan — 1 critical, 30 high, 23 medium, 14 low -->

**Generated (UTC):** 2026-08-31T23:13:55Z · **trivy 0.74.0** · **4 target(s)** (1 repo + 3 image(s))

Overwritten in place each week. Git history is the archive.

## Verdict

**1 critical, 30 high, 23 medium, 14 low** across all targets (70 total).

Most findings on a docker image are in the base operating-system packages of a
third-party image, not in anything this project wrote. The repo row is the one that
reflects our own dependency choices.

## By target

| Target | Critical | High | Medium | Low | Unknown |
|---|---|---|---|---|---|
| `repo: jahjah-internal (npm)` | 0 | 0 | 0 | 0 | 0 |
| `image: hello-world:latest` | 0 | 0 | 0 | 0 | 0 |
| `image: portainer/portainer-ce:lts` | 1 | 8 | 3 | 1 | 1 |
| `image: supabase/postgres:17.6.1.167` | 0 | 22 | 20 | 13 | 1 |

## Top items (critical and high only)

Showing up to 20 of 31 critical/high findings, critical first.

| Severity | Advisory | Package | Installed | Fixed in | Target |
|---|---|---|---|---|---|
| CRITICAL | `CVE-2026-56854` | `golang.org/x/crypto` | v0.54.0 | 0.55.0 | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2025-15558` | `github.com/docker/cli` | v28.5.1+incompatible | 29.2.0 | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-41567` | `github.com/docker/docker` | v28.5.2+incompatible | none yet | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-42306` | `github.com/docker/docker` | v28.5.2+incompatible | none yet | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-33747` | `github.com/moby/buildkit` | v0.25.1 | 0.28.1 | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-33748` | `github.com/moby/buildkit` | v0.25.1 | 0.28.1 | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-17106` | `github.com/moby/go-archive` | v0.1.0 | 0.3.0 | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-56864` | `golang.org/x/mod` | v0.37.0 | 0.40.0 | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-56865` | `golang.org/x/mod` | v0.37.0 | 0.40.0 | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-14456` | `libcrypto3` | 3.5.7-r0 | 3.5.8-r0 | `image: supabase/postgres:17.6.1.167` |
| HIGH | `CVE-2026-14456` | `libssl3` | 3.5.7-r0 | 3.5.8-r0 | `image: supabase/postgres:17.6.1.167` |
| HIGH | `CVE-2026-27145` | `stdlib` | v1.26.1 | 1.25.11, 1.26.4 | `image: supabase/postgres:17.6.1.167` |
| HIGH | `CVE-2026-32280` | `stdlib` | v1.26.1 | 1.25.9, 1.26.2 | `image: supabase/postgres:17.6.1.167` |
| HIGH | `CVE-2026-32281` | `stdlib` | v1.26.1 | 1.25.9, 1.26.2 | `image: supabase/postgres:17.6.1.167` |
| HIGH | `CVE-2026-32283` | `stdlib` | v1.26.1 | 1.25.9, 1.26.2 | `image: supabase/postgres:17.6.1.167` |
| HIGH | `CVE-2026-33810` | `stdlib` | v1.26.1 | 1.26.2 | `image: supabase/postgres:17.6.1.167` |
| HIGH | `CVE-2026-33811` | `stdlib` | v1.26.1 | 1.25.10, 1.26.3 | `image: supabase/postgres:17.6.1.167` |
| HIGH | `CVE-2026-33814` | `stdlib` | v1.26.1 | 1.25.10, 1.26.3 | `image: supabase/postgres:17.6.1.167` |
| HIGH | `CVE-2026-33818` | `stdlib` | v1.26.1 | 1.25.13, 1.26.6, 1.27.0-rc.3 | `image: supabase/postgres:17.6.1.167` |
| HIGH | `CVE-2026-39820` | `stdlib` | v1.26.1 | 1.25.10, 1.26.3 | `image: supabase/postgres:17.6.1.167` |

## What was scanned

- `trivy fs --scanners vuln` over `/root/jahjah-internal` — the npm dependency tree.
- `trivy image --scanners vuln` over every image in the local docker store.
- Only the vulnerability scanner runs. The secret scanner is deliberately off here —
  secrets are covered by `SCAN-gitleaks.md`, which is built to publish a hit without
  publishing the secret.
