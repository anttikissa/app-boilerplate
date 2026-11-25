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
- Home Screen web apps on iOS blur content under the status bar. Never
  set `apple-mobile-web-app-status-bar-style` to `black-translucent`,
  and add a fixed 11px top strip in the page color with
  `background-clip: text`, in standalone mode only.

## Web controls

The first time the app needs one of these, build it as an app
component; later uses reuse it.

- Dialogs: the `<dialog>` element, opened as a modal. Never `alert`,
  `confirm` or `prompt`: they title themselves with the domain
  ("example.com says") and block the page.
- A dialog opens with focus on its first input, or on the least
  destructive button for actions that can't be undone. Show the focus
  style only for keyboard use, never on touch devices. Review: open
  each dialog by tap with touch emulation and check that no focus style
  shows.
- Dropdowns: a `<select>` styled with customizable select
  (`appearance: base-select`). Chrome and Safari support it; Firefox
  shows its own list for now, with only the text of each option. Put
  spaces between the parts of a rich option (icon, name, description)
  so that text reads well.
- Numbers: a text input with `inputmode="numeric"`, parsed in code. A
  number input drops leading zeros, accepts "e" and changes its value
  on scroll.
- Dates and times: an app picker. Browser pickers follow the system
  locale and ignore the app's 24-hour clock setting.
- Tooltips: an app component that shows at once on hover and on
  keyboard focus. Never the `title` attribute: it shows after a long
  delay and never on touch. Add tooltips only where users will want
  more information, or where a control is unclear on first use but
  obvious afterwards. Touch has no hover, so needed information never
  lives only in a tooltip.

## Forms

- No browser validation: every form has `novalidate`, and the app
  shows its own message under each field.
- With more than one form, use one validation helper, such as
  `validate.ts` from https://github.com/anttikissa/building-blocks
  (copy the file). Define each schema once and use it on the server and
  the client, so the rules match. The server always validates again.
- Show errors when they help:
  - While the user types, unfinished values get no error. "Too short"
    after one character is wrong. Check when the user leaves the field,
    or on submit.
  - Once a field shows an error, check it on every keystroke, so the
    error goes away as soon as the value is fixed.
  - Run checks that only the server can do (for example, "name taken")
    while the user types. Send the request after a short pause (about
    300 ms), and ignore answers for older values.
  - On submit, check every field and move focus to the first error.
- Link each error to its field (`aria-describedby`), so screen readers
  read it.

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
