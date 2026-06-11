# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Starter Express REST API used as the base project for the Claude Code course. It is intentionally minimal — a real codebase to practice setting Claude up on, not a feature target. Avoid changing the app's behavior unless a task explicitly calls for it.

## Commands

- `npm install` — install dependencies
- `npm run dev` — run the API with auto-reload (`node --watch`) on http://localhost:3000
- `npm start` — run the API without watch
- `npm test` — run all tests (Node's built-in runner, `node --test`)
- `npm run lint` — run ESLint over the repo

Run a single test file or filter by name:

```
node --test tests/users.test.js          # one file
node --test --test-name-pattern="health" # tests whose name matches
```

CI (`.github/workflows/ci.yml`) runs `npm run lint` then `npm test` on Node 22 for every push and PR — keep both green.

## Architecture

Request flow: `server.js` → a router in `routes/` → `db/store.js`.

- **`server.js`** is the entry point. It builds the Express `app`, registers `express.json()`, and mounts one router per resource (`/users`, `/health`). It only calls `app.listen()` when run directly (`require.main === module`) and exports `app` otherwise — this lets tests import the app with supertest without opening a port. Preserve this guard.
- **`routes/`** holds one file per resource, each exporting an `express.Router()`. Route handlers own HTTP concerns (status codes, validation, JSON shape) and delegate all data work to the store.
- **`db/store.js`** is an in-memory stand-in for a database: a module-level `users` array plus `nextId`. State is not persisted and resets on every restart; tests rely on the seed data. All data access goes through its exported functions — routes never touch the array directly.

To add a resource: create `routes/<name>.js` exporting a router, mount it in `server.js`, and add any data helpers to `db/store.js`.

## Conventions

- CommonJS modules (`require` / `module.exports`), not ES module `import`. ESLint is configured with `sourceType: "script"`.
- Unused `req`/`res`/`next` args and any `_`-prefixed arg are allowed by lint; other unused vars warn. Don't leave new unused variables.
