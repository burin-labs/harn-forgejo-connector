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

## Pull request titles

Use `[Area] Sentence case`. The area is one of `Connector`, `CI`, or `Docs`.

- `[Connector] Reject webhook deliveries with a stale timestamp`
- `[CI] Repin the shared Harn package workflow`
- `[Docs] Describe the poll cursor contract`

Keep the title on one line, under about 70 characters. Say what changed, not
which files moved. Capitalize the first word after the bracket and leave the
rest in sentence case.

`CONTRIBUTING.md` states the contribution policy for this repository.

<!-- BEGIN HARN SHARED AGENT CONTRACT: managed by harn-bump-fleet -->

## Ecosystem working agreement

- Build ambitious outcomes behind small typed interfaces; give behavior one owner
  and generate or parity-test projections instead of duplicating policy.
- Work autonomously within approved scope. Pause for destructive or production effects,
  exceptional spend, material ambiguity, or new authority.
- Treat stop, wait, stand down, pivot, and steer as control events.
- Use the smallest owning product-path check. Add a falsifier for contested, load-bearing,
  or potentially vacuous claims; record controls, recovery, and blind spots.
- Evidence follows source/artifact identity. Reuse proof when relevant code, build inputs,
  and dependencies are unchanged. Repeat affected checks for relevant changes, failures,
  deployment, or packaging differences. Do not rebuild or recapture solely for main.
- Ship means owning-main integration with terminal merge and applicable release/deploy
  checks. Confirm landed content and result; an open PR is incomplete.
- Use `ship` with a deployed Smart Ship caller; otherwise use `gh pr merge --squash --auto`.
  Never use `--admin`; incidents use `bypass-ci`, `bypass-merge-queue`, or `force-merge`.

<!-- END HARN SHARED AGENT CONTRACT -->
