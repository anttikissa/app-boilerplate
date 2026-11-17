# TODO

Who: **Antti** writes it; **agent** drafts it, Antti reviews.

## Requested

- [ ] Server structure: folders, entry point, HTTP and WebSocket
      routes, one shared API type file for server and clients (agent)
- [ ] Deployment: one script; clone `origin/main` fresh, install,
      build, back up, migrate, smoke-test, restart; roll back on
      failure; keep the 5 newest old folders; prod runs from its own
      folder (agent, from `~/ghost/deploy`). Old-code check (decided),
      skipped with `-- breaking` and on the first deploy:
      1. Copy the database and migrate the copy
      2. Smoke-test the new code (`./next`) on port 0 against the copy
      3. Smoke-test the running release (`./app`) with its own
         `./test --smoke` on port 0 against the same copy
      4. If step 3 fails, stop: "The old release fails on the new
         schema. Add an expand step, or mark the migration
         `-- breaking`."

      Limit: it covers only the paths the old smoke test exercises
- [ ] Environments (decided):
      - Prod by default at `<service>.<root domain>`; a dev
        environment only when a change needs it, at
        `dev.<service>.<root domain>`, with its own data and env file
      - One flag: `--prod`; without it everything runs as dev. Every
        command takes it: `./dev` (dev only, hot reload), `./build`,
        `./run`, `./scripts/*`, `./deploy`
      - `./build` writes `dist-dev/`, `./build --prod` writes
        `dist-prod/`; the server serves only its own folder and stops
        if it is missing ("dist-prod/ not found. Run ./build --prod.")
      - Data and secrets live in the home folder, or on a mounted disk
        when large; never in `/var/lib`
      - Mobile Debug/Release builds follow `./dev`/`./build`; a hidden
        dev/prod server switch comes later, when a dev server exists
- [ ] Database: numbered migrations, `-- breaking` marker, backups
      before migrate (agent). SQLite (bun:sqlite) is the default for
      tests and small deployments; all SQL also runs on Postgres, so
      a project can move when it grows. When the potential scale is
      big (e.g. a chat app with >10,000 simultaneous users), consider
      sharding support from the start, but confirm with the user first.
      Compatibility (decided), ready for several servers at once:
      - Each migration works with the code version running before it
      - Rename: deploy 1 = expand migration (new column, copy, triggers
        between old and new) plus code that uses only the new column;
        deploy 2 = contract migration (drop triggers and old column).
        Drop a column: code stops using it, then a migration drops it.
        Copy Ghost's AGENTS.md section on expand and contract
      - `-- breaking` stays for changes that need every server stopped
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
- [ ] Tests (decided): one `./test` script sets the order and
      parallelism; `bun test` alone is not enough:
      - `./test`: `bun check` in parallel with `bun test --parallel`
      - `./test --smoke`: built server with `--prod` on port 0 and a
        throwaway database; deploy runs it before the restart; never
        sends email or push
      - `./test --expensive`: headless Chromium over raw CDP
      - `./test --all`: everything, fast tests first
      - `./test <file>`: one file
      - Tests read no env files; a test that needs a key sets a fake one
- [ ] Ids (decided): copy Ping `server/tid.ts` with a test; all ids
      use it. Server ids: monotonic ms + 2 random chars; client ids:
      4 random chars. Server inserts ids with plain `INSERT`, answers
      409 on collision ("This id exists. Make a new id and send
      again."), and the client retries with a new id; refuse ids more
      than 1 day in the future
- [ ] Auth: accounts, sessions; signup open to anyone unless the user
      asks for gating (Antti)
- [ ] Secrets (decided):
      - Gitignored `.env.dev` and `.env.prod`, mode 600, hold only
        secrets; all other config lives in committed `config.ts`
      - Every server file and script uses `import config from
        "config.ts"`; it reads `.env.dev`, or `.env.prod` with `--prod`.
        Bun's automatic `.env` loading is not used: keys stay out of
        `process.env`, so child processes do not inherit them, and prod
        never gets dev keys silently
      - Import stops only when the selected file does not exist:
        - only `.env.prod` and no `--prod`: "Only .env.prod is here.
          Add --prod to use prod."
        - neither file: "New checkout? Copy .env.example to .env.dev."
      - Empty or missing keys do not stop the app: startup logs one
        line naming them, and only code that reads such a key fails
        ("SMTP_KEY is not set in .env.dev"); keys absent from
        `.env.example` get a typo warning
      - Committed `.env.example` lists every key with an empty value
        (`SMTP_KEY=`) and a header comment with the reasons above
      - Server: the deploy links `~/<app>/.env.prod` into `app/`
      - Local against prod: copy `.env.prod`, run with `--prod`
- [ ] Push notifications: web push, APNs, FCM (agent, later)
- [ ] Client offline cache and scroll-position rule (agent)
- [ ] Region defaults: phone codes, 12/24-hour clock from the device
      (agent)
- [ ] Tooling: TypeScript config, `bunx oxfmt`, hook setup command
      (agent)
- [ ] New-project script: copy the boilerplate, rename, init Git and
      the GitHub repo (agent)
