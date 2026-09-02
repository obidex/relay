# Chunk 3 — the `main` repository ruleset

<!-- index: chunk 3 — ruleset 22122876 active on main: PR-only, squash-only, ci-ok required, no bypass actors -->

**Ruleset id:** **22122876** · [rules page](https://github.com/obidex/jahjah-internal/rules/22122876)
**Created (UTC):** 2026-09-02T15:46:35Z · **Enforcement:** `active` · **Decision:** `D228`

-----

## What it does

`main` (targeted as `~DEFAULT_BRANCH`) can now only be changed through a **squash-merged pull
request whose `ci-ok` check is green**. It cannot be force-pushed, cannot be deleted, cannot take a
merge commit, and **has no bypass actors** — the read-back reports `"current_user_can_bypass":
"never"` for the repository owner himself.

GATE 2's "merge only on green" has been a prompt-enforced rule for four chunks. It is now a property
of the repository: **a red pipeline cannot be merged by anyone.** The pre-authorized merge is
therefore safe by construction rather than by the implementer's discipline.

**Up-to-date enforcement is OFF on purpose** (`strict_required_status_checks_policy: false`). One
implementer merges sequentially, and the post-merge `main` run is watched by law — the rule that
already catches what `strict` would.

## Nothing was rejected

The API accepted every parameter as sent; no 422, no retry, nothing dropped.

## Read-back, verbatim

```json
{
    "id": 22122876,
    "name": "main",
    "target": "branch",
    "source_type": "Repository",
    "source": "obidex/jahjah-internal",
    "enforcement": "active",
    "conditions": {
        "ref_name": {
            "exclude": [],
            "include": [
                "~DEFAULT_BRANCH"
            ]
        }
    },
    "rules": [
        {
            "type": "deletion"
        },
        {
            "type": "non_fast_forward"
        },
        {
            "type": "required_linear_history"
        },
        {
            "type": "pull_request",
            "parameters": {
                "required_approving_review_count": 0,
                "dismiss_stale_reviews_on_push": false,
                "required_reviewers": [],
                "require_code_owner_review": false,
                "require_last_push_approval": false,
                "required_review_thread_resolution": false,
                "require_extra_approval_for_unattributed_changes": true,
                "allowed_merge_methods": [
                    "squash"
                ]
            }
        },
        {
            "type": "required_status_checks",
            "parameters": {
                "strict_required_status_checks_policy": false,
                "do_not_enforce_on_create": false,
                "required_status_checks": [
                    {
                        "context": "ci-ok"
                    }
                ]
            }
        }
    ],
    "node_id": "RRS_lACqUmVwb3NpdG9yec5Lbqs0zgFRkXw",
    "created_at": "2026-09-02T15:46:35.860Z",
    "updated_at": "2026-09-02T15:46:35.918Z",
    "bypass_actors": [],
    "current_user_can_bypass": "never",
    "_links": {
        "self": {
            "href": "https://api.github.com/repos/obidex/jahjah-internal/rulesets/22122876"
        },
        "html": {
            "href": "https://github.com/obidex/jahjah-internal/rules/22122876"
        }
    }
}
```

And the branch itself now reports the five rules in force:

```
$ gh api repos/obidex/jahjah-internal/rules/branches/main --jq '.[] | .type'
deletion
non_fast_forward
required_linear_history
pull_request
required_status_checks
```

## One thing GitHub added that we did not ask for

The read-back contains **`"require_extra_approval_for_unattributed_changes": true`** inside the
`pull_request` rule. It was not in the request body — GitHub supplies it as a default for that rule.

It matters because `required_approving_review_count` is `0`: if GitHub ever decides a commit in a PR
is "unattributed", that PR would need an approval that this single-operator setup has nobody to give.
Commits from this box carry the GitHub no-reply identity `144545793+obidex@users.noreply.github.com`,
which **is** attributed to the account, so it should never trigger — and `docs/pitfalls/infra-vps.md`
already requires that identity for a different reason (Vercel refuses to build an unattributed
commit while every CI job stays green).

**PR-B's merge is the live proof and it is not being pre-judged here.** If PR-B is refused for want
of an approval, this parameter is the cause and it is dropped in a one-line `PATCH`.

## Not tested with a push

Deliberately. No test push was made to `main` — that would either be refused (proving only that the
API works) or, worse, succeed. **PR-B's merge is the live proof**, and its report states whether the
merge went through the ruleset with `ci-ok` as the required context.

## Side effects checked

- **The relay is a different repository** (`obidex/relay`), so the fleet's eight publishers and the
  dispatch lane are untouched — they never push to `jahjah-internal`.
- **The dispatch lane is already forbidden** `git push` and `gh` by its own deny list, so it cannot
  have been relying on a direct push to `main`.
- **Dependabot** opens ordinary PRs, so the ruleset applies to it identically: green `ci-ok` or no
  merge.

```
=== RELAY ===
HEAD: b4040fe9112563d7829f13f56d41355414eac9e7 | tree: clean
CI: pass — main run 33648950432, 8/8 green including ci-ok
DONE: repository ruleset id 22122876 created and ACTIVE on main — PR-only, squash-only, single required check `ci-ok`, no force-push, no deletion, linear history, zero bypass actors (owner included). Read back in full; no parameter rejected.
FILES: 0 repo files changed (GitHub-side configuration) + 1 relay report
FINDINGS/BLOCKERS: GitHub silently defaults `require_extra_approval_for_unattributed_changes: true` inside the pull_request rule — not requested, and potentially blocking on a repo whose required approval count is 0. Commits here carry the attributed no-reply identity so it should never fire; PR-B's merge is the live proof and will say so either way.
NEXT-NEEDED: none — proceeding to T3 (GATE-1 hook)
=== END ===
```
