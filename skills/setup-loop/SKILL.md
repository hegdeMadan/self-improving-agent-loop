---
name: setup-loop
description: Set up the self-improving agent loop in the current repo - create docs/product/ with its project settings, constitution, north star, inbox, queue, metrics and history, filled in with the owner. Use when the user wants to start using the loop in a project.
---

# Set up the loop in this project

You create the files the loop runs on, fill in what the repo already tells you, and ask the owner only what it cannot.

## 1. Look before writing

- If `docs/product/project.md` exists, the loop is already set up: say so and stop, unless the owner asks to redo a part.
- Read the repo enough to fill settings: the package scripts or build files (the verify gate), the engineering standards (`CLAUDE.md` or similar), a decisions log, a design system or style files, how it deploys (`vercel.json`, a Dockerfile, CI workflows), and its main branch.

## 2. Create the files

Copy from this skill's `templates/` directory:

| Template | Goes to |
|---|---|
| `project.md` | `docs/product/project.md` |
| `constitution.md` | `docs/product/constitution.md` |
| `north-star.md` | `docs/product/north-star.md` |
| `inbox.md` | `docs/product/inbox.md` |
| `upstream.md` | `docs/product/upstream.md` |
| `metrics.csv` | `docs/product/metrics.csv` |
| `retro-changes.csv` | `docs/product/retro-changes.csv` |
| `history-README.md` | `docs/product/history/README.md` |

Also create `docs/product/queue/NEXT` containing `1` (or the next number after any existing numbered specs), and empty `docs/product/history/queue/`, `history/journal/`, `history/decisions/`, `history/design/` (each with a `.gitkeep`), `history/inbox-done.md` and `history/lessons.md`.

Add a `Read` deny rule to `.claude/settings.json` (create it, or merge into it) for every generated bulk file the repo holds: migration snapshots, lockfiles, build output, data dumps (for example `"permissions": {"deny": ["Read(/drizzle/meta/**)"]}`). Name each in the summary.

## 3. Fill in `project.md`

Fill every setting the repo answers, and mark each you inferred `(inferred)` so the owner can check it. Then ask the owner, in one short round, only what you could not infer:

- the verify gate, if no obvious command exists;
- whether the product has a UI, and the widths to check;
- where real data lives and what must never be copied into the repo;
- what the health check should look at on the live product (or "none yet");
- their time zone.

## 4. The north star and constitution

Ask the owner for one paragraph on what the product is for, the one number that says it works, and the first two or three slices. Write them into `north-star.md` in their words. Leave the constitution's rules empty unless they state some: rules are added when something goes wrong.

## 5. Engineering standards

If the standards file, the decisions log or the feature docs already pass their budgets, say so and offer an inbox item to rewrite them to budget, under the rule-preservation review in the run-loop skill's `process.md`.

If the project has no engineering standards file, say so: the engineer and the reviewer check against it. Offer to draft a short one from the code's existing patterns; do not write it without a yes.

Add one line to the standards file (or create `CLAUDE.md` with it): *When the owner asks for a change, reports a problem or shares an idea, record it with the `inbox` skill instead of editing code.*

## 6. The routines

A plugin cannot create scheduled routines. Tell the owner how, and offer to create them if this session has routine tools:

- **Loop:** hourly (e.g. `5 * * * *` UTC), this repo, prompt `Run the run-loop skill.`, with the strongest model.
- **Check-in:** hourly, offset from the loop (e.g. `45 * * * *`), prompt `Run the check-in skill.`. It is the only one that merges and the only one that can start the loop early, so it needs a session with the repo, GitHub and routine tools.

Both need the plugin available where they run. Record their ids in `project.md`'s `## Routines` table once created.

## 7. Commit

On a branch, `loop/setup`, staging the files by name. Open a PR if the owner wants one. Then tell the owner: what was created, which settings were inferred, and that the first request goes in with the `inbox` skill.
