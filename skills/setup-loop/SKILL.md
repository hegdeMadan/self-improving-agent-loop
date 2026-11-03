---
name: setup-loop
description: Set up the self-improving agent loop in the current repo - create docs/product/ with its project settings, constitution, north star, inbox, queue, metrics and history, filled in with the owner. Use when the user wants to start using the loop in a project.
---

# Set up the loop in this project

You create the files the loop runs on, fill in what the repo already tells you, and ask the owner only what it cannot.

## 1. Look before writing

- If `docs/product/project.md` exists, the loop is already set up: run **Upgrade** below instead of steps 2 to 6.
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

## Upgrade an existing setup

After the plugin is updated, running this skill again brings the project's loop files up to what this version expects. **It only adds what is missing; it never rewrites, reorders or removes what the owner or the retro wrote.**

1. Read the plugin version from `.claude-plugin/plugin.json` two folders above this skill's directory, and the project's `Loop version` row in `docs/product/project.md` (no row: the project was set up by 0.1.0).
2. **Settings:** compare `project.md`'s `## Settings` table with `templates/project.md`'s. Add each missing row with the value the repo answers or the default in the run-loop skill's `process.md`, marked `(inferred)` or `(default)`. Add each missing section heading of the template, empty. Set `Loop version` to this version.
3. **Files and folders:** create every file and folder step 2 of the setup lists that does not exist (with a `.gitkeep` in an empty folder). Add to `history/README.md` each line of `templates/history-README.md` it lacks.
4. **Read deny rules:** add any generated bulk file step 2 names that `.claude/settings.json` does not already deny.
5. **Move what the new rules move:** if the project has a UI, `git mv` the screen folders of all but the three most recently finished features to `docs/product/history/design/`. If the decisions log is past its cap, move its older entries, text unchanged, to `history/decisions/YYYY-MM.md`.
6. **Measure, do not rewrite:** the engineering standards and the feature docs against their budgets. Any over budget: offer an inbox item to rewrite it to budget under the rule-preservation review (`process.md`).
7. Commit on a branch, `loop/upgrade-<version>`, staging files by name, and open a PR if the owner wants one. Report each row, file, folder, rule and move added, and anything over budget. Nothing to add: say the project is already up to date.
