---
name: check-in
description: The loop's monitor - review and merge the PRs the loop opened, restart the loop straight after a merge, spot stalls and broken rules, and tell the owner only what matters. Use on an hourly check-in schedule or when the user asks how the loop is doing.
---

# Check in on the loop

The loop's routine cannot merge, and it cannot start itself. You can. Read the run-loop skill's `process.md` first, then `docs/product/project.md` and `docs/product/inbox.md`. Report times in the owner's time zone (`project.md`).

## What you decide alone, and what you ask

The line is **how the work travels** (yours) against **what gets built** (the owner's).

You do not ask before: merging a PR the rules below call mergeable; starting, pausing or re-pointing the loop's routine when its rules say to; fixing a rule in `project.md` or the loop's docs that is wrong, stale or contradicts another; reconciling claims and schedules. Do it, then say what you did and why in one short report.

You ask first for: anything that changes what the product does or shows (that goes to `inbox.md`); `constitution.md` and the north star's slices; dropping or re-scoping a PRD; deleting anything hard to undo (a routine: pause it instead; a branch with unmerged work; history). **A routine the owner paused stays paused**, however much work is waiting.

## Each check-in

1. **The routine.** List the loop's routines with whatever routine tools this session has. Did the last run succeed? Read a failed or odd run's session. Compare names, ids, schedules and enabled flags with what `project.md` records; correct the record where they differ, and say which changed.
2. **The repo.** `git fetch --prune`; find the feature branch (there is at most one); the queue state; the newest journal entries and `metrics.csv` rows; open PRs.
3. **Judge:** progressing, waiting normally, or stuck: the same state across runs, repeated failures, or a run that broke a rule (committed to main, merged a PR, force-pushed, wrong base, leaked something `project.md` says never to write).
4. **Fix only loop breakage:** a stale routine prompt, a broken rule, a retro edit its run could not save. Never write PRDs or application code here.
5. **Review, then merge.** For each new or updated PR, once per head commit:
   - Read it cold, as a second reviewer: CI on the head, mergeability, the diff against its PRD or brief, its verification. Post one review comment naming anything blocking (red CI, a conflict, a broken standard, missing grading).
   - **Mergeable** means: no conflict; CI green on the head (or none configured); the feature's file is in `docs/product/history/queue/` on the head; its `## Health after NN` is in its journal, and `## Retro after NN` when its header says `Retro: yes`; and any `## Review: open findings` or `## Code review: not completed` ruled on by you (each fixed through an engineer and reviewed again, or accepted with the reason in your comment).
   - Merge with a merge commit (never squash or rebase), pinned to the head sha. Never push to main yourself and never enable auto-merge.
6. **Straight after a merge, start the loop** so the next feature begins now rather than at the next scheduled run, unless the scheduled run is minutes away (two runs could start before either claims) or the routine is paused.
7. **Start the loop otherwise** when a `.ready.md`, `.building.md` or `.blocked.md` is waiting, or a finished feature lacks its grading, and the routine has not run for an hour.
8. **Tell the owner only what matters:** a feature built, a PR opened or merged, a real stall and what is needed from them, a broken rule, a question an agent is waiting on (scan `inbox.md` and the queue's `### Escalated` sections; mention each once, again only if it is still open a day later). When the stop condition is met, report the final state and ask whether to stop the check-ins; never delete them yourself.

## Running the loop's roles by hand

When the routine is paused and the owner asks for work to be done, you may run the roles yourself as the run-loop skill describes. Then you owe everything the routine would have done before the PR merges: every review round until one is nits-only or clean, the move to `history/queue/`, the health check and the retro when owed, the journal entry and the `metrics.csv` row (`routine` = `manual`).

## Small changes

A small, well-understood change the owner asks for in chat may skip the queue: brief an `engineer` directly (in a worktree), then a `code-reviewer` pass on its branch before merging, with the same review loop. A finding that needs a product decision goes to `inbox.md`. Run the verify gate yourself before merging.

Whenever you spawn a loop agent yourself, give it the absolute path of the run-loop skill's `process.md`, as the coordinator does.
