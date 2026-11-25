# Decisions

By topic. Each entry: the decision, then the reason when it is not
obvious.

## Environments

- Production only by default, at `<service>.<root domain>`; it may
  move to its own domain later. A dev environment is optional, made
  only when a change needs testing outside production, at
  `dev.<service>.<root domain>`. A `/dev` path is out: absolute URLs,
  cookie paths and the service worker scope break.
- One flag, `--prod`, for every command; without it everything runs
  as dev.
- Data and secrets live in the home folder, or on a mounted disk when
  large; never in `/var/lib`.
- Configuration for every environment is in the committed
  `config.ts`. Secrets are in gitignored `.env.dev` and `.env.prod`:
  secrets only, no configuration. `config.ts` reads them; Bun's
  automatic `.env` loading is not used, so keys stay out of
  `process.env` and child processes.
- Running locally against production: copy `.env.prod` and run with
  `--prod`.

## Accounts

- Signup is open to anyone by default. Gating only when the user asks.

## Clients

- A web app installable to the home screen (iOS and Android) and an
  iOS app with feature parity; Android later.
- `design/` is the spec of every client and the source of truth, as in
  Ping: each spec names its code on a `Code:` line, and the commit-msg
  hook fails a code change without its spec change.
- iOS has an offline cache from the start: the app opens on the last
  state before the network answers.
- English from the start; localization later. Settings have a 24-hour
  clock option from the start, defaulting to the device setting. At
  project start, ask the user about language and region defaults.
- Push notifications are built in when the app needs them. The
  permission prompt comes only from a user action that needs it, never
  on first load.
- No browser alert, confirm or prompt dialogs. They title themselves
  with the domain ("example.com says"), ignore the app's style and
  block the page. The first time the app needs a question or a notice,
  build an in-app dialog component on the native `<dialog>` element;
  every later one reuses it.

## Updates

- Web clients detect a new server version and show a pill:
  `( Update app | What's new? | X )`. Update app reloads; What's new
  shows the changelog (first the git log, later friendlier); X closes
  it and a toast says the update stays available in Settings, which
  has the same controls and changelog. Home screen apps have no reload
  button, so this is their only way to update.
- The server tells its version (short commit hash) in an API call and
  at the start of each live event stream.

## Live updates

- App state syncs in real time from the start: an edit on one client
  shows on the others.
- Server-sent events plus plain HTTP requests, unless the app needs
  two-way messages on one connection. `EventSource` reconnects by
  itself and sends `Last-Event-ID`, which is the catch-up cursor.
- One change log table with a sequence number; every write adds to it.
  Live events tail it; `GET /api/changes?since=<seq>` serves catch-up.
- Client state: one store keyed by id; each event replaces the item.
  Web: Solid 2 store. iOS: `@Observable`. Android: Compose state.

## Database

- Numbered migrations. A `-- breaking` migration stops the server
  before it runs; other migrations run while the old server serves.
  Expand-contract migrations are declared explicitly.
- Back up before migrating. When a backup takes over 2 s, it may be
  skipped for trivial migrations.

## Deployment

- One process, zero downtime without two servers: Caddy retries the
  closed port (`lb_try_duration 30s`) while the app restarts, then
  shows a bundled 503 page. On SIGTERM the app closes its port, lets
  open requests finish, and exits; systemd kills it after 10 s.
- Build and smoke-test in `next/`, move `app/` to
  `attic/app-<commit>`, move `next/` to `app/`, migrate, start. Keep
  the 5 newest folders in the attic. Roll back on failure.
- The 503 page reloads every 0.5 s. API requests get JSON with status
  503 and `Retry-After`; every client obeys it.

## Logs

- Plain lines with ISO timestamps, one file per day in
  `/var/log/<app>/`. A log service with a viewer and search comes
  later.
- At start, without delaying it, the server logs its sizes: logs,
  database, stored files, server code and client bundles.

## Tooling

- `./cli` runs one command (`./cli seed`) or, alone, opens a REPL with
  help and tab completion. `sqlite` opens `sqlite3` on the configured
  database.
- New projects: an agent follows `NEW-PROJECT.md`; no script.
