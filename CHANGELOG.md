# Changelog

## Unreleased

- Security sweep 2026-05-23 (hardening, fail-closed defaults):
  - **F1 (CRITICAL) SSRF:** `__api_url` no longer treats absolute-URL `path` arguments as an
    escape from the configured `api_base_url`.
  - **F3 (HIGH) fail-closed:** webhook delivery is now rejected with 401 `missing_signature`
    when no signing secret is configured for the binding.
  - **F5 (HIGH) trusted secret resolution:** `__signing_secret` no longer reads attacker-
    controlled `raw.*` fields. Secrets resolve from orchestrator `ctx`, the per-binding state
    seeded via `activate()`, or `$FORGEJO_SIGNING_SECRET` only.
  - **F6 (HIGH) body source:** signature verification runs against `body_text`, else the
    decoded bytes of `body_base64`. Absent body now rejects 400 `missing_body`.
  - **F7 (MEDIUM) exact event match:** `__supports_event` matches exact names or fully-
    qualified subkinds; bare prefix matches are gone.
  - **F8 (MEDIUM) require binding_id:** inbound requests without a `binding_id` are rejected
    with 400 `missing_binding_id`.
  - **F9 (MEDIUM) dedupe key:** derived from the host-set `x-gitea-delivery` /
    `x-forgejo-delivery` headers only.
  - **F13 (LOW) shutdown gating:** documented `@host_only` semantics for `shutdown()`.
  - **F14 (LOW) verify before parse:** signature verification runs ahead of `json_parse`.
  - Deferred: F12 (switch outbound dispatch to `connector_http_request`), F15 (bump-harn
    workflow scoping).
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
