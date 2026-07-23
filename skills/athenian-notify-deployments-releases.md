---
name: Notify Athenian of deployments and releases
description: Push deployment and release events into Athenian from CI/CD so they appear in deployment and release analytics, then verify via the filter endpoints.
api: openapi/athenian-openapi-original.yml
operations: [notify_deployments, notify_releases, filter_deployments, filter_releases]
---

# Notify Athenian of deployments and releases

Athenian ingests deployment/release events pushed from your pipeline (inbound notifications, not consumer webhooks).

## Auth
- Write endpoints require `Authorization: Bearer <JWT>` (Auth0). API keys are read-only.
- `account` is required (auto-injected into the body when omitted).

## Steps
1. **`notify_deployments`** — `POST /events/deployments`. Report each deployment: `name`, `components[]` (repository + resolved commit), `environment`, `date_started`, `date_finished`, `conclusion`, optional `labels`.
2. **`notify_releases`** — `POST /events/releases`. Report releases not captured from git tags/branches: `repository`, `commit`, `name`, `author`, `published`.
3. **`filter_deployments`** — `POST /filter/deployments` to confirm ingested deployments appear.
4. **`filter_releases`** — `POST /filter/releases` to confirm releases appear.

## Conventions
- Errors use the `GenericError` (RFC 7807-style) envelope — see `conventions/athenian-conventions.yml` and `errors/athenian-problem-types.yml`.
- A `424 Failed Dependency` indicates a required integration (GitHub/JIRA) is not yet connected for the account.

> Note: Athenian has wound down; `api.athenian.co` no longer resolves. Documented here for reference / self-hosting.
