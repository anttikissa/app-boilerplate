# App boilerplate

A starting point for apps with a server, a web client, an iOS client
and later an Android client. A new project copies it and replaces this
paragraph with what the app is. Read DECISIONS.md before changing
behavior. Record each new decision there, by topic.

## General coding guidance

Climb the lazy ladder: skip code that need not exist; prefer the
standard library, native platform features (Bun, Web APIs), installed
dependencies, one line, then the minimum code that works. Never cut
trust-boundary validation, data-loss handling, security,
accessibility or explicit requirements.

Use as few dependencies as possible, above all ones that reach users
(the server at runtime, the client bundle); dev-only type packages
such as `@types/bun` are fine. No new dependency without the user's
approval.

Avoid defensive, just-in-case code. Rate how often each case occurs:
always, often, sometimes, rarely, almost never or practically never.
For a case rated "rarely" or below, ask the user whether to handle it
before you write code for it.

Generalize every bug fix. Find the root cause: the design choice or
missing rule that made this bug possible. Search for its other
instances on every client, fix them, or list them for the user if the
fix is not straightforward. Then add a rule to this file (or the spec)
that prevents the whole class of bugs. State to the user which rule
you added. If no rule can prevent it, say why.

Start each source file with a comment that says why the module exists.

Ids come from `tid()`, secrets (sessions, tokens) from `secretId()`.
Never use UUIDs or a hand-written random string. Raw random bytes are
only for crypto keys, IVs, salts and protocol fields.

Make random ids and secrets as short as their use allows. Size each one
from how many valid values exist, how long they live and how fast an
attacker can guess (rate limits cut the odds a lot); a temporary image
URL needs only enough characters to stop guessing. One-time codes can
be shortest: 4 base32 characters have the entropy of 6 digits.

Encode alphanumeric ids and codes in Crockford base32: write lower
case, read leniently (any case; o as 0; i and l as 1).

Store and send data as ASON. Use UUIDs or JSON only where an external
system's protocol requires them.

Stack: Bun, bun:sqlite, TypeScript. Format with `bunx oxfmt`.
Typecheck with `bun check`.

## Specific coding details

Reference rules for agents (humans can skip this section).

Open every SQLite database with `PRAGMA busy_timeout=5000;` BEFORE
`PRAGMA journal_mode=WAL; PRAGMA synchronous=NORMAL;`. Without
`busy_timeout` set first, concurrent openers crash with
`SQLITE_BUSY_RECOVERY` during WAL initialization/recovery; without
WAL+NORMAL, the default mode waits for the disk on every write
(35 ms per UPDATE measured on a network volume) and blocks the thread
meanwhile.

In the web client, trap and wrap `Tab` / `Shift-Tab` across visible
focusable descendants of the topmost open `dialog:modal` so focus
never escapes to `document.body` or lands on the `<dialog>` container
itself in WebKit or Safari.

In the SolidJS web client, every controlled `<select>` whose
`<option>` or `<optgroup>` children are rendered dynamically
(`<For>` / `<Show>`) must set `selected={...}` on each `<option>` AND
sync `el.value` in a `createEffect` tracking both the selected value
and the options list, because replacing `<option>` DOM nodes resets
the browser's `selectedIndex` to `0`.

For web keyboard shortcuts, use `Cmd` (without `Ctrl`) on macOS and
`Ctrl` elsewhere rather than `e.metaKey || e.ctrlKey` so macOS Emacs
editing keys (`Ctrl+A/E/K/B/L/P/N`) stay free; never intercept
browser-owned shortcuts (`Cmd/Ctrl+L`, `R`, `T`, `W`); and never
hijack editor keys in capture-phase listeners when an editable
element is focused.

## Tests

`./test` runs `bun check` beside `bun test --parallel`; run it for
every change.

Tests are maintenance debt; add one only if it catches a plausible
regression worth its cost. Keep full suites for self-contained modules
that could be libraries, for contracts between parts (API, stored
data, sync) and for trust boundaries. Write no tests for experimental
code; all UI code starts experimental.

Tests never use the network. Delete a flaky test; never retry it,
lengthen its timeout or skip it. Test behavior, not source text.

## Git

Commit as soon as each change works and is verified; don't leave
finished work uncommitted. Several agents share this checkout, so
stage and commit only your own changes: name files explicitly (never
`git add -A` or `.`), and when a file also holds someone else's
edits, stage only your hunks.

Wrap commit messages at 72 columns. End with these trailers:

    Implemented by: <model id>
    Session: <session id>

Keep in sync with GitHub: after every commit, run `git pull --rebase`
and then `git push`.

The repository is public. Never write a private host name or a path on
someone's machine (a home folder, a checkout) into a file, a commit or
a commit message. Use example.com in examples.

## Secrets

Secrets live in gitignored `.env.dev` and `.env.prod` (mode 600),
which hold only secrets; `config.ts` reads them. Never read them,
print them or log their values; the server logs only the names of
missing keys.
