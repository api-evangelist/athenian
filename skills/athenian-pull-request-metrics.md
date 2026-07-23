---
name: Calculate pull request delivery metrics
description: Compute DORA-style pull-request metrics (lead time, cycle time, WIP, review time) for an Athenian account over a time range and team, then drill into the underlying PRs.
api: openapi/athenian-openapi-original.yml
operations: [calc_metrics_prs, filter_prs, paginate_prs]
---

# Calculate pull request delivery metrics

Athenian is a full-cycle software-development analytics API. Metrics are always scoped to an `account`.

## Auth
- Send `X-API-Key: <token>` (read endpoints) or `Authorization: Bearer <JWT>` (Auth0).
- The server injects `account` into the request body if you omit it.

## Steps
1. **`calc_metrics_prs`** — `POST /metrics/pull_requests`. Body: `account`, `date_from`, `date_to`, the `metrics[]` you want (e.g. `pr-lead-time`, `pr-cycle-time`, `pr-wip-time`, `pr-review-time`), `granularities[]`, and `for[]` filters (repositories, developers, teams).
2. **`filter_prs`** — `POST /filter/pull_requests` to list the actual PRs behind a metric for the same window/filters.
3. **`paginate_prs`** — `POST /paginate/pull_requests` first when the PR set is large; it returns the pagination plan (date boundaries) to feed back into `filter_prs`.

## Conventions
- Errors follow the RFC 7807-style `GenericError` envelope (`type`/`title`/`status`/`detail`, plus `pointer` on `InvalidRequestError`) — see `errors/athenian-problem-types.yml`.
- No idempotency-key contract; these are read/compute POSTs and are safe to retry.

> Note: Athenian has wound down; `api.athenian.co` no longer resolves. This skill documents the published contract for reference / self-hosting from the open-source `athenian-api-open`.
