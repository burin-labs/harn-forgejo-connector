# harn-forgejo-connector

Pure-Harn Forgejo connector: signed webhooks, self-hosted REST dispatch, and
raw API helpers.

This package implements the Harn Connector interface contract v1 for `forgejo`.
It normalizes inbound webhook payloads to the tagged `NormalizeResult` envelope,
verifies provider-specific webhook signatures, and exposes outbound raw API helpers
plus common PR/comment/status method aliases.

## Install

```sh
harn add github.com/burin-labs/harn-forgejo-connector@v0.1.0
```

Use a path checkout for unreleased `main` or local multi-repo development:

```toml
[dependencies]
harn-forgejo-connector = { path = "../harn-forgejo-connector" }
```

## Webhook verification

Forgejo webhooks must be signed. Configure `signing_secret` or
`forgejo/webhook-secret`; the connector verifies the `x-gitea-signature`
HMAC-SHA256 header against the raw request body and rejects requests with no
configured secret, missing binding id, missing signature, or invalid signature.

## Authentication

Outbound calls use a token accepted by the Forgejo instance API. Pass
`access_token`, `token`, `personal_access_token`, or `app_password` in the call
args, or set the `FORGEJO_TOKEN` environment variable.

## Rate limiting and pagination

Outbound requests pass through a preemptive token bucket from
`std/connectors/shared` (default 60 req/min, configurable via
`rate_limit = { capacity, refill_tokens, refill_interval_ms }`; pass
`rate_limit = { disabled = true }` to bypass it). Responses are inspected for
`x-ratelimit-remaining`/`x-ratelimit-reset` and `429`, with a single retry once
the reset window elapses.

`pull_requests.list` and `issues.list` page through results using
`paginate_cursor`. They follow `Link: <...>; rel="next"` when Forgejo returns
one, falling back to `?page=` when a full page is returned without a `Link`
header.

## Development

```sh
harn check src/lib.harn
harn fmt --check src/lib.harn tests/*.harn
for test in tests/*.harn; do harn run "$test" || exit 1; done
```

## License

Dual-licensed under MIT and Apache-2.0.
