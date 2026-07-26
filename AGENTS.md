# AGENTS.md

Pure-Harn connector package for Forgejo and Codeberg-style deployments.

Shared connector authoring rules live in the Harn guide:

- [Connector authoring guide](https://github.com/burin-labs/harn/blob/main/docs/src/connectors/authoring.md)

Put shared connector guidance in the Harn guide and keep only
provider-specific notes and local hazards here.

`CLAUDE.md` points here. Edit `AGENTS.md` only.

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

<!-- BEGIN HARN SHARED AGENT CONTRACT: managed by harn-bump-fleet -->

## Ecosystem working agreement

- Pursue the ambitious product outcome; make the seams boring with small typed
  interfaces, explicit invariants, and deterministic projections.
- Give each behavior one semantic owner. Generate or parity-test other surfaces
  instead of maintaining competing implementations.
- Work autonomously inside approved scope. Pause for destructive, production,
  high-spend, ambiguous, or authority-expanding actions—not routine reversible work.
- Treat stop, wait, stand down, and pivot as control events for long-lived work.
- Match evidence to the claim: exercise the canonical user path, state the
  falsifier, verify liveness and recovery, and record residual blind spots.
- "Ship" means landed on main with required deploy and post-merge checks complete.

<!-- END HARN SHARED AGENT CONTRACT -->
