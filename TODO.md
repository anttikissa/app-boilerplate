# TODO

Who: **Antti** writes it; **agent** drafts it, Antti reviews.

## Requested

- [ ] Server structure: folders, entry point, HTTP and WebSocket
      routes, one shared API type file for server and clients (agent)
- [ ] Deployment: one script; clone `origin/main` fresh, install,
      build, back up, migrate, smoke-test, restart; roll back on
      failure; keep the 5 newest old folders; prod runs from its own
      folder (agent, from `~/ghost/deploy`)
- [ ] Environments: dev, prod (staging?); ports, data folders, config
      by command-line flags and gitignored files (Antti)
- [ ] Database (bun:sqlite): schema, numbered migrations, `-- breaking`
      marker, backups before migrate (agent)
- [ ] Design spec (`design/`) and STYLE.md: spec is the source of
      truth; `Code:` lines; commit-msg hook that fails code changes
      without spec changes (agent drafts the frame, Antti the taste)
- [ ] iOS, Android and web apps: folder per client, how specs reach
      every client, "tested" trailers for clients this host cannot
      build (Antti decides scope, agent drafts)

## Proposed

- [ ] AGENTS.md: commit trailers, bug-class rule, defensive-code
      rating, lazy ladder, no new dependencies without approval
      (agent, from Ping and Hal)
- [ ] DECISIONS.md: decisions by topic (agent)
- [ ] Sync: live events as a fast path; catch-up of every change
      (edits, deletes, not only new rows) after reconnect (agent)
- [ ] Tests: `./test` fast, `./test-expensive` headless Chromium over
      raw CDP; smoke test on a throwaway database (agent)
- [ ] Auth: accounts, sessions, signup policy (Antti)
- [ ] Secrets: where they live, how agents stay out of them (Antti)
- [ ] Errors and logs: every failure reaches the user complete;
      server log location and rotation (agent)
- [ ] Push notifications: web push, APNs, FCM (agent, later)
- [ ] Client offline cache and scroll-position rule (agent)
- [ ] Region defaults: phone codes, 12/24-hour clock from the device
      (agent)
- [ ] Tooling: TypeScript config, `bunx oxfmt`, hook setup command
      (agent)
- [ ] New-project script: copy the boilerplate, rename, init Git and
      the GitHub repo (agent)
