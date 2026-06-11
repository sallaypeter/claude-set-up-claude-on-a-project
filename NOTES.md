# NOTES

## What did you put in your CLAUDE.md, and what did you deliberately leave out, and why?

I included the things Claude can't reliably infer from a quick scan of the repo: the exact commands (`npm run dev`, `npm test`, `npm run lint`, and how to run a single test), the request flow (`server.js` → `routes/` → `db/store.js`), the non-obvious `require.main === module` guard that lets tests import `app` without opening a port, the fact that `db/store.js` is an in-memory store that resets on restart and that tests depend on its seed data, and the recipe for adding a new resource. I also noted the project conventions (CommonJS, not ESM) and the lint rule about unused args.

I deliberately left out a file-by-file walkthrough, dependency listings, and anything already visible in `package.json` or the source itself — duplicating that just rots as the code changes. I also left out feature roadmaps and app behavior changes, because this is a teaching base project that's meant to stay minimal, so I added a "don't change app behavior unless the task calls for it" guardrail instead.

## Which permission rules did you add, and what could go wrong without your deny rule?

**Allow:** the everyday safe commands — `npm test`, `npm run test:*`, `npm run lint`, `npm run dev`, and `node --test:*` — so routine test/lint/dev loops don't trigger a prompt every time.

**Ask:** `git push:*`, so a push always pauses for confirmation rather than going out silently.

**Deny:** reading `.env` / `.env.local`, and `git push --force` / `git push -f`.

Without the deny rules: reading `.env` files could leak secrets (API keys, credentials) into the conversation/context where they don't belong. And a `git push --force` could overwrite shared branch history on the remote — clobbering teammates' commits with no easy recovery. Denying these outright (rather than just "ask") means they can't happen even by accident or by an over-eager automated step.
