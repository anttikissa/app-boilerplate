# App boilerplate

A starting point for apps with a server, a web client, an iOS client
and later an Android client. A new project copies it and replaces this
paragraph with what the app is. Read DECISIONS.md before changing
behavior. Record each new decision there, by topic.

## Code

Climb the lazy ladder: skip code that need not exist; prefer the
standard library, native platform features (Bun, Web APIs), installed
dependencies, one line, then the minimum code that works. Never cut
trust-boundary validation, data-loss handling, security,
accessibility or explicit requirements.

No new dependencies without the user's approval. Propose one only with
its exact line count, including all transitive dependencies, with the
version and the counting method.

Avoid defensive, just-in-case code. Rate how often each case occurs:
always, often, sometimes, rarely, almost never or practically never.
For a case rated "rarely" or below, ask the user whether to handle it
before you write code for it.

Every bug fix starts with its class. Find the root cause: the design
choice or missing rule that made this bug possible, not only the line
that failed. Search for its other instances on every client and fix
them, or list them for the user. Then add a rule to this file (or the
spec) that prevents the whole class, and say in the reply which rule
you added. If no rule can prevent it, say why.

Start each source file with a comment that says why the module exists.

Stack: Bun, bun:sqlite, TypeScript. Format with `bunx oxfmt`.
Typecheck with `bun check`.

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
