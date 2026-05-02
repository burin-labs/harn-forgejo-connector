# CLAUDE.md - harn-forgejo-connector

Pure-Harn connector package for Forgejo and Codeberg-style deployments.

Shared Harn connector authoring rules live in the canonical guide:

- https://github.com/burin-labs/harn/blob/main/docs/src/connectors/authoring.md

Keep this file limited to provider-specific notes and local hazards. Add shared connector guidance
to the Harn guide first.

## Provider Notes

- Webhook event names use `x-gitea-event`; delivery ids may be `x-gitea-delivery` or `x-forgejo-
  delivery`.
- Webhook signatures use the Gitea-compatible `x-gitea-signature` HMAC scheme when a signing secret
  is configured.
- Outbound calls default to the Codeberg API URL, but self-hosted Forgejo instances must pass an
  `api_base_url` and an accepted access token or PAT.
