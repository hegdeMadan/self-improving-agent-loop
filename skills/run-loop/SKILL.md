---
name: run-loop
description: Run one pass of the self-improving agent loop on this repo - grade the last finished feature, then carry one piece of work from the inbox or north star to an open, reviewed PR. Use when the check-in starts the loop's routine, or when the user asks to run the loop or the pipeline by hand.
---

# Run the loop

You are the coordinator. You spawn the agents, move files between states, keep the record and open the PR. **You never write application code, PRDs or screens yourself.**

Read first, in this skill's directory: `process.md` (the shared rules) and `metrics.md` (the run record). Then, in the project: `docs/product/project.md` and `docs/product/constitution.md`.

Give every agent you spawn the absolute path of this skill's `process.md`: the agents' files refer to it.

If `docs/product/project.md` does not exist, the project has not been set up: say so, point at the `setup-loop` skill, and stop.

Record `date -u` now, for `duration_min`.

If the start message carries owner answers, record each in `docs/product/inbox.md` (or where the message says) and commit it with this run's first push.

If the stop condition (`process.md`) is met, append the run summary and stop.

Find the feature branch (`process.md`, "One feature at a time"). Work there, or on the main branch when there is none. Never commit to main, never merge a PR, never force-push.

- **It exists, and its work is done** (in `history/queue/`) **with its PR open, and the start message asks for a fix-only run** → step 0 for it if its grading is missing, then spawn the engineer with the findings the PR leaves open (or, for `## Code review: not completed`, go straight to the review). Then one code-review round on the new commits, as step 7 runs it but past the round limit, appended to `history/queue/NN-slug.md`'s `## Code review`. Update the PR body's `## Review: open findings` (removed when the round is clean), record, and stop.
- **It exists, and its work is done with its PR open**, otherwise → run step 0 for it if its grading is missing, then record `noop`, `waiting on PR #n`, and stop.
- **It exists, `.building.md` at phase `built` or `review` with a dead claim** → take the claim over and resume at step 7, from the last round its `## Code review` records.
- **It exists, anything else** → continue it from the step its state names.
- **None exists** → start at step 0.

## Steps

**0. Grade the last finished feature, before any new work.**
   - 0a. **Health check**, always, when its journal has no `## Health after NN`: spawn `retro` with the health-check task, the feature's number, and nothing else. When `project.md`'s `## Health check` defines none, spawn nothing: write `## Health after NN — not checked: project defines no health check` yourself.
   - 0b. **Learning retro**, when its journal has no `## Retro after NN` and one of these reasons holds; otherwise write `## Retro after NN — skipped: no reason`, naming the reasons checked:
     - code review raised a blocking finding;
     - a review should-fix or validator finding is of a kind already in `history/lessons.md` or covered by a written rule in `project.md` (an existing rule failed);
     - the owner reported a miss since the last retro;
     - a role section of `project.md` is over its word budget (consolidate first).
     Spawn `retro` with the learning task and the feature's number: a fresh agent, given only what `retro`'s file names, never your account of the run.
   - If the stop condition is now met, append the run summary and stop.

**1. Manager.** Spawn `manager`. It picks the next piece of work and rules on its path and retro. If it reports nothing to do, record `noop` and stop. New work starts its branch from main.
   - Short-path brief (`.ready.md`) → step 5.
   - Routed to pm-author → step 2.

**2. pm-author.** It writes the routed PRD, revises a returned `.draft.md`, or answers a `.blocked.md`.

**3. ui-designer**, when pm-author left a `.draft.md` and `project.md` says the project has a UI. Left a `.ui.md` (the owner's screens cover it), or the project has no UI → rename `.draft.md` to `.ui.md` yourself if needed, and go to 4.

**4. pm-validator** on the `.ui.md`. Returned to `.draft.md` → pm-author revises against the findings, ui-designer redraws if it left a `.draft.md`, pm-validator checks again. At most the revision limit (`project.md`, default 2). Still returned after that → append `## Unresolved after N revisions` with the open findings, rename to `.ready.md`, and say so prominently.

**5. Engineer** on the `.ready.md`. It claims, builds and runs the verify gate.

**6. A block is answered in this run.** Spawn pm-author on the `.blocked.md` with the PRD and the engineer's questions, nothing else.
   - Answered → it renames to `.ready.md`; commit, push, back to 5. The engineer claims it afresh.
   - The answer changes a screen → ui-designer redraws, pm-validator re-checks, then back to 5.
   - Escalated to the owner → commit the `.blocked.md`, push, record `blocked`, stop.

**7. Code review, before any PR.** Only once the verify gate passes; if it fails and the engineer could not fix it, leave `.building.md`, push, report, stop. Set the phase to `review`, push, and spawn `code-reviewer` with the PRD, the head sha and this checkout's path, nothing else.
   - After each round, append to the `.building.md` under `## Code review`: `### Round N · reviewed <sha>`, the findings verbatim, and the engineer's reply to each (fixed in `<sha>`, or disputed with evidence). Push.
   - Blocking or should-fix findings → spawn the engineer with them. Nits are not fixed and never start a round: list them in the journal as left. It fixes, reruns the gate, pushes. Set `review` again and spawn code-reviewer on the new commits alone, given the `## Code review` section and the last reviewed sha.
   - The loop ends on a round with only nits or nothing, or after the round limit (`project.md`, default 3), counted from the `### Round` headings so a takeover or a block does not reset them. Findings open after the last round go in the PR body under `## Review: open findings`; the PR is not mergeable until a fix-only run closes them or the owner accepts them.
   - A review that returns no ranked report is not a pass: spawn it once more on the same sha; if that fails too, the PR says `## Code review: not completed` with the reason.
   - A finding that needs a decision the PRD does not make is a block: step 6.
   - Then move the file to `docs/product/history/queue/NN-slug.md` (drop the claim line). If the project has a UI, keep in the design folder the screens of the three most recently finished features (this one included) and `git mv` any older feature's folder to `docs/product/history/design/`. Commit and push.

**8. Open the PR** from the feature branch to main. The body: what it implements, its verification, `## Code review` (each round, what was found and fixed, what the reviewer checked and found sound), and `## Owner setup` for any new secret or setting. Never merge it, never enable auto-merge.

**9. Grade it now**, so the PR is mergeable when it opens: step 0a always, 0b when owed. Step 0 of the next run is the backstop.

**10. Record.** Append to `docs/product/history/journal/NN-slug.md`: which work, the path and retro ruling, what the validator raised, what the engineer decided alone, blocks and how they were answered, each review round. Append this run's `metrics.csv` row. Commit and push.

## In a cloud routine

- Plugins don't load in cloud sessions: the routine prompt clones this loop and reads its files by path, and spawns each role as a general-purpose agent whose instructions are its agent file.
- The GitHub proxy rejects GraphQL, so `gh pr create` and `gh pr merge` fail: open and read PRs through REST or the session's GitHub tools.
- Routines push to `claude/` branches unless told otherwise: the prompt names the feature branch prefix and forbids moving work to a `claude/` branch.
- Before any agent runs, prepare what the verify gate needs (the pinned runtime, private package access, a local database for the tests) and never relax a pin to get past one. If one cannot be had, change nothing, report it and stop: never skip a test for a missing service.

## Every run, whatever happened

Exactly one `metrics.csv` row, a `noop` included. Commit and push it on the branch you worked on (main only when the run touched no feature branch and the project allows it; otherwise on a `loop/metrics-<date>` branch the check-in merges).
