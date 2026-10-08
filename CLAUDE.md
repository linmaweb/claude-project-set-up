# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

A minimal Express API (starter project for the Claude Code course) exposing `/users` and `/health`, backed by an in-memory store.

## Commands

- `npm run dev` — start the API on http://localhost:3000 with auto-restart
- `npm test` — run all tests (Node's built-in test runner + supertest)
- `npm run lint` — check code style with ESLint
- `node --test tests/users.test.js` — run a single test file

## Conventions

- Use CommonJS (`require`/`module.exports`), not ES modules — matches the rest of the codebase.
- Use Node's built-in `node:test` + `assert` + `supertest` for tests, not Jest or Mocha.
- Route handlers return early with `res.status(...).json(...)` on error/not-found cases, instead of nesting the success path in an `else`.
- All data access goes through `db/store.js` — route handlers never read or mutate the `users` array directly.

## Architecture

- `server.js` — entry point; builds the Express app and mounts routers. Exports `app` without calling `.listen()` when required (e.g. from tests), only starts listening when run directly.
- `routes/` — one router file per resource (`users.js`, `health.js`), mounted in `server.js`.
- `db/store.js` — in-memory data access layer; all route handlers go through this rather than touching data directly. State resets on every restart.
- `.env` holds real config/secrets and is git-ignored; `.env.example` documents the shape.
