# Weekly committed-secret scan (gitleaks)

<!-- index: weekly gitleaks scan — ZERO secrets found in either repo history -->

**Generated (UTC):** 2026-09-07T04:00:04Z · **gitleaks 8.30.1** · **full history of 2 repositories**

Overwritten in place each week. Git history is the archive.

## Verdict

**ZERO secrets found.** Explicitly: gitleaks read every commit in the full history of both
the ERP repo and the public relay repo — 813 commits in total — and found no key, token,
password or connection string in any of them.

## By repository

| Repository | Result | History read |
|---|---|---|
| jahjah-internal (ERP, private) | clean | 154 commits |
| relay (public) | clean | 659 commits |

## Findings

None.

## What was scanned

- `gitleaks git` over the FULL history of `/root/jahjah-internal` and `/opt/jahjah/relay`.
- Full history, not the working tree: a secret removed in a later commit is still in the
  repository and still has to be rotated.
- The same scanner runs as a blocking gate on every pull request, so a new secret is
  caught at the PR rather than waiting for this weekly pass.
