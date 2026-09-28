# Weekly dependency + image scan (trivy)

<!-- index: weekly trivy scan — 0 critical, 11 high, 11 medium, 5 low -->

**Generated (UTC):** 2026-09-28T03:00:04Z · **trivy 0.74.0** · **2 target(s)** (1 repo + 1 image(s))

Overwritten in place each week. Git history is the archive.

## Verdict

**0 critical, 11 high, 11 medium, 5 low** across all targets (28 total).

Most findings on a docker image are in the base operating-system packages of a
third-party image, not in anything this project wrote. The repo row is the one that
reflects our own dependency choices.

## By target

| Target | Critical | High | Medium | Low | Unknown |
|---|---|---|---|---|---|
| `repo: jahjah-internal (npm)` | 0 | 0 | 1 | 0 | 0 |
| `image: portainer/portainer-ce:lts` | 0 | 11 | 10 | 5 | 1 |

## Top items (critical and high only)

Showing up to 20 of 11 critical/high findings, critical first.

| Severity | Advisory | Package | Installed | Fixed in | Target |
|---|---|---|---|---|---|
| HIGH | `CVE-2025-15558` | `github.com/docker/cli` | v28.5.1+incompatible | 29.2.0 | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-41567` | `github.com/docker/docker` | v28.5.2+incompatible | none yet | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-42306` | `github.com/docker/docker` | v28.5.2+incompatible | none yet | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-33747` | `github.com/moby/buildkit` | v0.25.1 | 0.28.1 | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-33748` | `github.com/moby/buildkit` | v0.25.1 | 0.28.1 | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-17106` | `github.com/moby/go-archive` | v0.1.0 | 0.3.0 | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-56854` | `golang.org/x/crypto` | v0.54.0 | 0.55.0 | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-56864` | `golang.org/x/mod` | v0.37.0 | 0.40.0 | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-56865` | `golang.org/x/mod` | v0.37.0 | 0.40.0 | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-84304` | `google.golang.org/grpc` | v1.82.1 | 1.83.1 | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-84445` | `google.golang.org/grpc` | v1.82.1 | 1.82.2, 1.83.2, 1.84.0-dev.0.20260825144003-d5a41119e0e3, 1.85.0-dev.0.20260825072537-93e31b48545e | `image: portainer/portainer-ce:lts` |

## What was scanned

- `trivy fs --scanners vuln` over `/root/jahjah-internal` — the npm dependency tree.
- `trivy image --scanners vuln` over every image in the local docker store.
- Only the vulnerability scanner runs. The secret scanner is deliberately off here —
  secrets are covered by `SCAN-gitleaks.md`, which is built to publish a hit without
  publishing the secret.
