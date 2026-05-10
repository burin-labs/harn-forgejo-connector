# Changelog

## Unreleased

- Adopt `std/connectors/shared` for HMAC signature verification, the preemptive token-bucket
  rate limiter, and cursor pagination. Reactive handling now respects Forgejo's
  `x-ratelimit-remaining`/`x-ratelimit-reset` headers and retries once on `429`.
- Add `pull_requests.list` and `issues.list` outbound methods that paginate via
  `Link: rel="next"` with a `?page=` fallback.
- Declare `capabilities = ["webhook", "rate_limit", "pagination"]` on the provider entry.
- Drop the unreachable SourceHut Ed25519 branch and the SVN `poll_tick` placeholder that were
  left over from the connector template.

## v0.1.0

- Initial pre-alpha Forgejo connector package implementing Harn Connector contract v1.
