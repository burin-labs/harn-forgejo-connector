# SKILL: harn-forgejo-connector

Use `harn-forgejo-connector` when wiring Harn triggers or outbound helpers for Forgejo.

## What you get

- Provider id: `forgejo`
- Trigger kinds: `webhook`
- Supported events: `push, pull_request, issues, issue_comment, release, repository, star`
- Webhook verification: `gitea_hmac`
- Outbound helpers: `api.request`, `pull_requests.comment`, `pull_requests.update`, `issues.comment`, `commit_status.set`, `repository_file.get`

## Trigger recipe

```toml
[[triggers]]
id = "forgejo-events"
kind = "webhook"
provider = "forgejo"
match = { path = "/hooks/forgejo", events = ["pull_request.opened"] }
handler = "handlers::on_forgejo_event"
secrets = { signing_secret = "forgejo/signing-secret" }
```

For SourceHut, use a configured `public_key` secret instead of `signing_secret`.
For SVN polling, add a `poll` trigger and run `harn connector check . --provider svn --run-poll-tick`.
