# The process

The rules every part of the loop shares. The coordinator (`SKILL.md`) and every agent follow them; where an agent's own file states one of these rules, it states this one.

## The project's files

All state lives in the project's repo, never in a session. A run starts with no memory of earlier runs and reconstructs everything from these files.

```
docs/product/
  project.md        how this project runs the loop: settings, data rules, lessons per role
  constitution.md   the owner's standing rules (few, and only the owner adds one)
  north-star.md     what the product is for and its slices, in order
  inbox.md          the owner's open requests
  queue/            work in flight only, plus NEXT (the next free number)
  metrics.csv       one row per run
  retro-changes.csv one row per change a retro made, scored by the next retro
  upstream.md       proposed changes to the loop itself, for the plugin's repo
  history/          finished work: queue/, journal/, inbox-done.md, lessons.md
```

**`history/` is out of the read path.** No agent reads or searches it unless its task is about the past (a retro reading its own feature's journal, a question like "why was this removed"). Then it searches, and never reads a file whole.

## The read path stays small

The loop writes to the files every run reads: the engineering standards, the decisions log, the feature docs, `project.md`. Unchecked, they grow until every run wades through history (one project's journal reached 375,000 words before a cleanup). Each has a budget in `project.md`, or the default below when `project.md` has no row for it, and these rules keep it there as the work happens, so no big cleanup is ever needed:

- **Decisions log:** newest first, each entry at most about 6 lines. It keeps the entries of the last two weeks, and never fewer than the 15 newest; whoever adds an entry past that moves the older ones, text unchanged, to `history/decisions/YYYY-MM.md` in the same commit.
- **Structure doc:** where code lives now, one line per folder or file that needs one; a change that adds, moves or removes one updates it in the same branch.
- **Feature docs:** what the product does **now**, in the present tense. No "since PRD N" history; each section names the PRD that last shaped it, once. A change that makes a line untrue corrects that line in the same branch.
- **Engineering standards:** today's rules only. Changing a rule rewrites it in place; the date, the reason and the old wording go to the decisions log, never into the standards.
- **Generated bulk files** (migration snapshots, lockfiles, build output, data dumps) are denied to `Read` in `.claude/settings.json`, so no search wades through them.
- **A rewrite to get back under budget changes no rule.** It is its own PR, and its review maps every rule (each must, never, only, number and name) in the old file to its line in the new one. A rule the rewrite would drop or change because it no longer holds goes to the owner as an inbox item before the PR merges; the rewrite never decides it.

## Defaults

A setting `project.md` does not have (a project set up by an older version of the loop) takes its default. Never stop for a missing row; say in the journal that the default was used.

| Setting | Default |
|---|---|
| Main branch | `main` |
| Feature branch prefix | `feature/` |
| Claim timeout | 40 minutes |
| Heartbeat | 30 minutes |
| Validator revisions | 2 |
| Review rounds | 3 |
| Widths checked | 390, 1280 |
| Design folder | `docs/design/` |
| Word budget per role section | 400 |
| Engineering standards budget | 3,500 words |
| Feature docs budget | 12,000 words |
| Decisions log cap | the last two weeks, and never fewer than the 15 newest entries |
| Structure doc | none |

## The queue

One file per piece of work, `docs/product/queue/NN-slug.<state>.md`. The state is the suffix. Rename to advance; never copy.

| State | Means | Who moves it there |
|---|---|---|
| `.draft.md` | PRD written | pm-author |
| `.ui.md` | screens attached | ui-designer, or pm-author when the owner's own screens cover every state |
| `.ready.md` | validated, or a short-path brief | pm-validator; the manager for a brief; pm-author after answering a block |
| `.building.md` | claimed by a run | engineer |
| `.blocked.md` | the engineer found a question the spec does not answer | engineer |
| done | merged-ready | the coordinator moves it to `history/queue/NN-slug.md` once code review ends |

A validator that returns a PRD renames it back to `.draft.md` with `## Validator findings` appended.

**Numbering.** `docs/product/queue/NEXT` holds the next free number. Whoever creates a queue file takes that number and writes the next one in the same commit. Check the main branch and any open feature branch first; if one already used the number, take the next free one.

## Two paths

The manager rules on every piece of work before anything starts.

- **Short path:** the manager writes a brief as `.ready.md`; the engineer builds it, code-reviewer reviews it. No PRD, no screens, no validator.
- **Full path:** pm-author writes a PRD, ui-designer draws it (when the project has a UI and the owner has not drawn it), pm-validator checks it, the engineer builds it, code-reviewer reviews it.

The manager also rules `**Retro:** yes` or `no` (the learning retro). The health check runs after every finished piece of work whatever the ruling.

## One feature at a time

- Each piece of work lives on its own branch, `<branch prefix>NN-slug` (`project.md`), created from the main branch.
- At most one such branch is open. No run starts new work while one exists, or while its PR is open.
- **Find the feature branch first:** `git fetch origin`; the current one is the branch with the prefix on origin that is not yet merged into main (`git merge-base --is-ancestor` fails). There is at most one.
- The routine never merges and never pushes to main. Every PR targets main; the check-in merges.

## Never run the same work twice

A `.building.md` carries its claim in its first line:

```
**Claimed by:** <session id> at <UTC> · phase: <phase> since <UTC>
```

- **Claiming is a push, and the push is the lock.** Write the claim, commit, and `git push` without force, before any work. If the push is rejected because the branch moved, another run claimed it first: fetch and obey the claim you find.
- **Phases:** `claimed`, `prd`, `screens`, `validated`, `building`, `built`, `review`. Rewrite the one line at every change of phase and push it.
- **Heartbeat:** a long step rewrites the line's `since` to now and pushes it whenever the heartbeat interval (`project.md`, default 30 minutes) has passed since its last push.
- **A claim is live** while its phase is younger than the claim timeout (`project.md`, default 40 minutes). Not yours and live: exit as `noop`, `another run in progress`. Not yours and older: the run died; take it over by rewriting the line with your session id, phase and the time now.
- The test is the phase's age, never the branch's last push: a healthy build goes a long time without one.
- A `.building.md` with no claim line may be taken over.

## Blocking

`.blocked.md` is not a human gate. pm-author answers it in the same run, moments later, and the engineer is spawned again on the `.ready.md` it leaves. The cost of blocking is minutes; **the bar for blocking does not move**. What waits for the owner is only a question only the owner can settle: pm-author appends `### Escalated` and the run stops.

**Never guess to keep moving.** An agent that cannot settle something from the constitution, the PRD or the code writes it down and changes the file's state. A blocked item is a healthy outcome. A plausible invention is not: it is invisible until the owner trips over it.

## Handing work to an agent

Give each agent its task and the files it names, never your own account of the run. An agent asked to grade or answer something gets the inputs, not a summary of them: the summary is one of the things it exists to check.

## Models

Each agent's default model is the `model:` line in its file. Overrides go in `project.md`. A cheaper model is enough for drawing screens, a narrow revision against one or two findings, and the coordinator's bookkeeping (git, journal, metrics). Writing a new PRD, every validation, every engineer run that writes code, every code review and every retro take the strongest model. Record what ran on what in `metrics.csv`'s `models` column.

## Commits

A folder under `docs/product/history/` that a step moves or writes into and that does not exist yet is created in the same commit (`mkdir -p`), never a reason to stop.

Stage files by name, never `git add -A` or `git add .`: another agent's worktree inside the repo gets staged as a gitlink. Push with `git push -u origin <branch>`, never with force. Commit messages end with the session's attribution lines.

## Stop condition

The loop stops when all of these hold:

1. every slice in `north-star.md` is marked `✅ built`;
2. `queue/` holds nothing but `NEXT`;
3. the last finished feature has its health check, and its learning retro when one was owed;
4. `inbox.md` has no open item that is not `PARKED`.

Then the run appends the run summary (`metrics.md`) and stops. A slice blocked on data only the owner can provide is not a stop: it stays blocked, the journal names exactly what it needs, and the loop carries on with whatever else it can answer.
