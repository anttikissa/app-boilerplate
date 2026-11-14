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
- [ ] Database: numbered migrations, `-- breaking` marker, backups
      before migrate (agent). SQLite (bun:sqlite) is the default for
      tests and small deployments; all SQL also runs on Postgres, so
      a project can move when it grows. When the potential scale is
      big (e.g. a chat app with >10,000 simultaneous users), consider
      sharding support from the start, but confirm with the user first
- [ ] Design spec (`design/`) and STYLE.md: spec is the source of
      truth; `Code:` lines; commit-msg hook that fails code changes
      without spec changes (agent drafts the frame, Antti the taste)
- [ ] iOS, Android and web apps: folder per client, how specs reach
      every client, "tested" trailers for clients this host cannot
      build (Antti decides scope, agent drafts)
- [ ] Agentic affordances: agents use the app as well as build it
      (agent drafts from Ping, Antti picks):
      - Use: `/llms.txt` guide (signup, auth, rules, limits),
        discoverable from the page (`service-doc` link, `Link` header,
        1 px body link), 404 for unknown paths; full API reference at
        `/api.md`; plain JSON API with sentence errors; bearer tokens
        per program, revoked one by one, untouched by password change;
        long poll `GET /api/wait` and HMAC-signed webhooks; rate limits
        that answer 429 with the reason
      - Build: an agent can run, inspect and verify the app alone; CLI
        to query state, dev login without secrets, seed data, readable
        logs, screenshots of each client
- [ ] Logging: how to do it; format (structured or plain lines),
      levels, where logs live per environment, rotation, how agents
      and people read them; every failure reaches the user complete
      (agent drafts from Ping `server/log.ts` and Hal, Antti picks)
- [ ] ASON and other building blocks: copy proven self-contained
      modules with their test suites; start with Hal's
      `src/common/ason.ts` and `src/host/live-file.ts`, then survey
      Hal and Ping for more (agent lists, Antti picks)

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
- [ ] Push notifications: web push, APNs, FCM (agent, later)
- [ ] Client offline cache and scroll-position rule (agent)
- [ ] Region defaults: phone codes, 12/24-hour clock from the device
      (agent)
- [ ] Tooling: TypeScript config, `bunx oxfmt`, hook setup command
      (agent)
- [ ] New-project script: copy the boilerplate, rename, init Git and
      the GitHub repo (agent)
