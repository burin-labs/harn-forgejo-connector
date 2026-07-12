# CLAUDE.md - harn-forgejo-connector

Pure-Harn connector package for Forgejo and Codeberg-style deployments.

Shared Harn connector authoring rules live in the canonical guide:

- https://github.com/burin-labs/harn/blob/main/docs/src/connectors/authoring.md

Keep this file limited to provider-specific notes and local hazards. Add shared connector guidance
to the Harn guide first.

## Provider notes

- Webhook event names use `x-gitea-event`; delivery ids may be `x-gitea-delivery` or
  `x-forgejo-delivery`.
- Webhook signatures use the Gitea-compatible `x-gitea-signature` HMAC scheme when a signing
  secret is configured. Verification delegates to `verify_hmac_signature` from
  `std/connectors/shared`.
- Outbound calls default to the Codeberg API URL, but self-hosted Forgejo instances must pass an
  `api_base_url` and an accepted access token or PAT.
- Outbound rate limiting is layered: a preemptive token bucket from
  `std/connectors/shared::rate_limit_token_bucket` (defaults to Forgejo's 60 req/min) plus
  reactive handling of `x-ratelimit-remaining`/`x-ratelimit-reset` and `429` responses with a
  single retry. Tests pass `rate_limit = { disabled = true }` to bypass the preemptive bucket.
- List endpoints (`pull_requests.list`, `issues.list`) page through results using
  `paginate_cursor`. They follow `Link: <...>; rel="next"` when Forgejo returns one and fall
  back to incrementing `?page=` when a full page is returned without a `Link` header.
