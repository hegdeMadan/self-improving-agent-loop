# self-improving-agent-loop

A product team of Claude agents that carries each request from an inbox to a reviewed pull request, one feature at a time, and **rewrites its own rules after every feature** from what it measured.

A Claude Code plugin. It grew out of a real project, where it shipped more than a hundred specs end to end, and was extracted so any project can run it.

## The loop

```
owner's request ──► inbox ──► manager ──┬─ short path ─► brief ──────────────────────┐
                                        │                                            ▼
north-star slice ───────────────────────┴─ full path ─► pm-author ─► ui-designer ─► pm-validator
                                                          ▲  (answers blocks)          │
                                                          │                            ▼
                                                       engineer ◄──────────────── ready
                                                          │
                                                          ▼
                                              code-reviewer (up to 3 rounds)
                                                          │
                                                          ▼
                                       PR ─► health check + learning retro ─► check-in merges
                                                          │
                                         lessons into project.md, scored next time
```

| Agent | Does |
|---|---|
| `manager` | picks the next work; rules short path (writes the brief itself) or full path; rules whether a retro follows |
| `pm-author` | writes the PRD with an edge-case table; revises against findings; answers the engineer's blocks in the same run |
| `ui-designer` | draws every state at every width as PNGs (projects with a UI) |
| `pm-validator` | checks the PRD against the code: every finding cites a PRD line and a contradicting file line |
| `engineer` | claims, tests first, builds, runs the verify gate; **blocks rather than guesses** |
| `code-reviewer` | adversarial, read-only; reports only what it reproduced, ranked blocking / should-fix / nit |
| `retro` | health check of the live product after every feature; learning retro that scores the last retro's changes and writes new rules |

| Skill | Does |
|---|---|
| `run-loop` | one pass of the loop: grade the last feature, then carry one piece of work to a reviewed PR |
| `check-in` | the monitor: review and merge PRs, restart the loop after a merge, spot stalls, report what matters |
| `inbox` | record a request with a severity instead of editing code |
| `setup-loop` | create the loop's files in a project and fill in its settings |

## How it improves itself

- **Every retro change is a hypothesis.** It goes in `retro-changes.csv` with the metric that should move; the next retro scores it `helped`, `no_effect`, `hurt` or `untestable`.
- **Lessons are rules, not stories.** Each is one sentence in the right role's section of the project's `project.md`; the incident behind it goes to `history/lessons.md`.
- **Rules are pruned, not only added.** Every fifth retro, or when a section passes its word budget, the retro merges duplicates and removes rules that scored `no_effect` or `hurt` twice.
- **What holds for every project goes upstream.** The retro proposes it in the project's `upstream.md`; the owner brings it here as a PR. That is also how contributions arrive.

## Principles

- **All state is in the repo.** Each run starts with no memory; the queue, the journal and the branch are the truth, so a run cut off resumes on the next one.
- **Never guess to keep moving.** A blocked item is a healthy outcome; a plausible invention is not.
- **One feature at a time.** One branch, one PR, merged, then the next. A claim line with a phase heartbeat keeps two runs off the same work.
- **History is out of the read path.** Finished work lives in `docs/product/history/`, searched only when a question is about the past.
- **Agents grade inputs, not accounts.** The retro and the reviewer never get the coordinator's summary of the run.

## Install

```
/plugin marketplace add <owner>/self-improving-agent-loop
/plugin install self-improving-agent-loop@self-improving-agent-loop
```

Then in your project: run the `setup-loop` skill. It creates `docs/product/`, fills in what it can from the repo, and asks for the rest.

## Updating

```
/plugin marketplace update self-improving-agent-loop
/plugin update self-improving-agent-loop@self-improving-agent-loop
/reload-plugins
```

Then run the `setup-loop` skill in each project that uses the loop: it adds only what the new version expects (settings rows, folders, read rules) and changes nothing you or the retro wrote. A project not yet upgraded still works: a missing setting takes the default in `skills/run-loop/process.md`.

## Running it

- **By hand:** run the `run-loop` skill.
- **Unattended:** two scheduled routines, an hourly loop (`Run the run-loop skill.`) and an hourly check-in offset from it (`Run the check-in skill.`). The routines need the plugin available where they run; `setup-loop` explains the steps.

## In a project

```
docs/product/
  project.md        settings, data and deploy rules, health check, lessons per role
  constitution.md   the owner's few standing rules
  north-star.md     purpose, measure, slices
  inbox.md          open requests
  queue/            work in flight, and NEXT
  metrics.csv       one row per run
  retro-changes.csv every retro change and its score
  upstream.md       proposed changes to this loop
  history/          finished PRDs, journals, inbox-done, lessons
```

## Status

Early (0.2.1). Proven in its original project; not yet run on a second one. Not yet licensed for reuse.
