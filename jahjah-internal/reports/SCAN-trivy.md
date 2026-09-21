# Weekly dependency + image scan (trivy)

<!-- index: weekly trivy scan — 13 critical, 376 high, 531 medium, 344 low -->

**Generated (UTC):** 2026-09-21T03:00:04Z · **trivy 0.74.0** · **8 target(s)** (1 repo + 7 image(s))

Overwritten in place each week. Git history is the archive.

## Verdict

**13 critical, 376 high, 531 medium, 344 low** across all targets (1288 total).

Most findings on a docker image are in the base operating-system packages of a
third-party image, not in anything this project wrote. The repo row is the one that
reflects our own dependency choices.

## By target

| Target | Critical | High | Medium | Low | Unknown |
|---|---|---|---|---|---|
| `repo: jahjah-internal (npm)` | 0 | 0 | 1 | 0 | 0 |
| `image: portainer/portainer-ce:lts` | 0 | 11 | 8 | 5 | 1 |
| `image: public.ecr.aws/supabase/gotrue:v2.196.0` | 0 | 26 | 13 | 17 | 1 |
| `image: public.ecr.aws/supabase/postgres:17.6.1.166` | 0 | 38 | 50 | 17 | 7 |
| `image: public.ecr.aws/supabase/postgres-meta:v0.96.1` | 9 | 104 | 140 | 106 | 4 |
| `image: public.ecr.aws/supabase/realtime:v2.130.0` | 3 | 122 | 242 | 169 | 4 |
| `image: public.ecr.aws/supabase/storage-api:v1.73.1` | 1 | 37 | 27 | 13 | 0 |
| `image: supabase/postgres:17.6.1.167` | 0 | 38 | 50 | 17 | 7 |

## Top items (critical and high only)

Showing up to 20 of 389 critical/high findings, critical first.

| Severity | Advisory | Package | Installed | Fixed in | Target |
|---|---|---|---|---|---|
| CRITICAL | `CVE-2023-45853` | `zlib1g` | 1:1.2.13.dfsg-1 | none yet | `image: public.ecr.aws/supabase/postgres-meta:v0.96.1` |
| CRITICAL | `CVE-2026-13221` | `perl-base` | 5.36.0-7+deb12u3 | none yet | `image: public.ecr.aws/supabase/postgres-meta:v0.96.1` |
| CRITICAL | `CVE-2026-31789` | `libssl3` | 3.0.18-1~deb12u2 | 3.0.19-1~deb12u2 | `image: public.ecr.aws/supabase/postgres-meta:v0.96.1` |
| CRITICAL | `CVE-2026-31789` | `openssl` | 3.0.18-1~deb12u2 | 3.0.19-1~deb12u2 | `image: public.ecr.aws/supabase/postgres-meta:v0.96.1` |
| CRITICAL | `CVE-2026-33845` | `libgnutls30` | 3.7.9-2+deb12u6 | 3.7.9-2+deb12u7 | `image: public.ecr.aws/supabase/postgres-meta:v0.96.1` |
| CRITICAL | `CVE-2026-42010` | `libgnutls30` | 3.7.9-2+deb12u6 | 3.7.9-2+deb12u7 | `image: public.ecr.aws/supabase/postgres-meta:v0.96.1` |
| CRITICAL | `CVE-2026-42496` | `perl-base` | 5.36.0-7+deb12u3 | none yet | `image: public.ecr.aws/supabase/postgres-meta:v0.96.1` |
| CRITICAL | `CVE-2026-59873` | `tar` | 6.2.1 | 7.5.19 | `image: public.ecr.aws/supabase/postgres-meta:v0.96.1` |
| CRITICAL | `CVE-2026-8376` | `perl-base` | 5.36.0-7+deb12u3 | none yet | `image: public.ecr.aws/supabase/postgres-meta:v0.96.1` |
| CRITICAL | `CVE-2026-13221` | `perl-base` | 5.40.1-6 | 5.40.1-6+deb13u1 | `image: public.ecr.aws/supabase/realtime:v2.130.0` |
| CRITICAL | `CVE-2026-42496` | `perl-base` | 5.40.1-6 | 5.40.1-6+deb13u1 | `image: public.ecr.aws/supabase/realtime:v2.130.0` |
| CRITICAL | `CVE-2026-8376` | `perl-base` | 5.40.1-6 | 5.40.1-6+deb13u1 | `image: public.ecr.aws/supabase/realtime:v2.130.0` |
| CRITICAL | `CVE-2026-59873` | `tar` | 7.5.11 | 7.5.19 | `image: public.ecr.aws/supabase/storage-api:v1.73.1` |
| HIGH | `CVE-2025-15558` | `github.com/docker/cli` | v28.5.1+incompatible | 29.2.0 | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-41567` | `github.com/docker/docker` | v28.5.2+incompatible | none yet | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-42306` | `github.com/docker/docker` | v28.5.2+incompatible | none yet | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-33747` | `github.com/moby/buildkit` | v0.25.1 | 0.28.1 | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-33748` | `github.com/moby/buildkit` | v0.25.1 | 0.28.1 | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-17106` | `github.com/moby/go-archive` | v0.1.0 | 0.3.0 | `image: portainer/portainer-ce:lts` |
| HIGH | `CVE-2026-56854` | `golang.org/x/crypto` | v0.54.0 | 0.55.0 | `image: portainer/portainer-ce:lts` |

## What was scanned

- `trivy fs --scanners vuln` over `/root/jahjah-internal` — the npm dependency tree.
- `trivy image --scanners vuln` over every image in the local docker store.
- Only the vulnerability scanner runs. The secret scanner is deliberately off here —
  secrets are covered by `SCAN-gitleaks.md`, which is built to publish a hit without
  publishing the secret.
