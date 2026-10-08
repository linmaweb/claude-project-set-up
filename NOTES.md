# NOTES.md

## What did you put in your CLAUDE.md, and what did you deliberately leave out, and why?

**Put in:**
- A one-line description so Claude knows what the project is without exploring first.
- Commands (`npm run dev`, `npm test`, `npm run lint`, running a single test file) — these are run every session and save Claude from guessing or checking `package.json` each time.
- Conventions (CommonJS over ES modules, `node:test`/supertest over Jest, early-return error handling, all data access through `db/store.js`) — these aren't enforced by linting and aren't obvious from any single file, so stating them up front keeps Claude's generated code consistent with the existing style instead of introducing a second pattern.
- Architecture notes on `server.js`'s dual role (entry point vs. exported app for tests) and the one-router-per-resource layout — this took reading three files to piece together, which is exactly the kind of thing worth summarizing once.

**Left out, and why:**
- Anything derivable from a quick read of the code (e.g. route list, field names) — Claude can read the files directly, so listing them would just be a second copy to go stale.
- One-off or task-specific notes — those belong in a conversation or a PR description, not a file Claude loads every session.
- Secrets or env values — `.env.example` already documents the shape, and real secrets never belong in a committed file.

## Which permission rules did you add, and what could go wrong without your deny rule?

**Rules added** (in `.claude/settings.json`):
- **Allow**: `npm test`, `npm run lint` — safe, read-only-in-effect commands I run constantly; no reason to be prompted every time.
- **Ask**: `git push` — common enough that an outright deny would be annoying, but I want a chance to glance at what's being pushed before it leaves my machine.
- **Deny**: reading `./.env`, `git push --force`, `rm -rf` — a secrets leak and two hard-to-reverse/destructive actions.

**What could go wrong without the deny rule:**
- Without blocking `./.env`, Claude could read real credentials straight out of the file and potentially echo them into a response, a log, or a commit.
- Without blocking `git push --force`, Claude could overwrite shared history on the remote, destroying teammates' commits with no easy recovery.
- Without blocking `rm -rf`, Claude could recursively delete files or directories with no undo.

None of these three are needed for normal development on this project, so the cost of blocking them outright is low.

## Verification

- `/memory` shows `CLAUDE.md` loaded in a fresh session.
- `/permissions` shows the allow (`npm test`, `npm run lint`), ask (`git push`), and deny (`./.env`, `git push --force`, `rm -rf`) rules.
- Asking "How do I run the tests here?" is answered with `npm test` pulled from `CLAUDE.md`, with no extra explanation needed.
