# Metrics

The journal records *why*; these files record the *numbers*, so whether the loop is improving rests on evidence rather than its own impression.

## `docs/product/metrics.csv`: one row per run

Every run appends exactly one row, including runs that did nothing. Leave a cell empty when it does not apply; never guess a number.

| Column | Meaning |
|---|---|
| `run_at_utc` | ISO time the run started |
| `routine` | `loop` (a routine run), `manual` (the loop run by hand), or `retro` (a row the retro writes) |
| `session_url` | the run's session link, if known |
| `branch` | the branch worked on |
| `feature` | `NN-slug`, or empty |
| `outcome` | `noop`, `retro`, `drafted`, `revised`, `designed`, `validated_pass`, `validated_return`, `unresolved_ready`, `answered_block`, `building`, `blocked`, `done`, `checks_failed`, `stopped` |
| `state_before` / `state_after` | queue state before and after, e.g. `draft` → `ready` |
| `path` | `short` or `full` |
| `validator_findings` | findings raised this run |
| `revision_round` | 0, 1, 2… |
| `block_questions` | questions under `## Blocked` this run |
| `decisions_left_open` | points the engineer settled alone (its journal's **Decided** bullets) |
| `review_rounds` | code-review rounds this run |
| `review_findings` | blocking + should-fix findings across those rounds |
| `tests_added` / `tests_total` / `tests_passed` | from the verify gate |
| `files_changed` / `lines_added` / `lines_removed` | `git diff --shortstat` of the run's commits |
| `screens` | images added or changed |
| `duration_min` | wall-clock minutes, from `date -u` at start and end |
| `notes` | at most 12 words, no commas |
| `models` | `role:model` pairs separated by spaces, every agent spawned |

## `docs/product/retro-changes.csv`: one row per retro change

Every change a learning retro makes is a testable hypothesis, and the next learning retro scores it. This is the loop's evidence that it is learning.

| Column | Meaning |
|---|---|
| `id` | `R<NN>-<n>`, e.g. `R12-1` |
| `retro_after` | the feature the retro followed |
| `file` | file edited |
| `change` | one line |
| `evidence` | the journal line, finding or commit that motivated it |
| `failure_case` | the closest case from the retro's list, or `none` |
| `hypothesis` | what should measurably differ next time |
| `metric` | the `metrics.csv` column or observation that will show it |
| `result` | filled by the next retro: `helped`, `no_effect`, `hurt` or `untestable` |
| `result_evidence` | filled by the next retro: the numbers or line behind the verdict |

## Stats per feature

Every learning retro appends `### Stats for NN` under its entry: runs by outcome, wall-clock time from first draft to done, validator returns, blocks and how long each took to answer, decisions the engineer made alone, review rounds and findings, tests added, lines changed, and where the work came from (north-star slice or inbox items).

## Run summary

When the stop condition is met, the run appends `## Run summary` to `docs/product/history/journal/loop.md`: per feature, runs by outcome, time to done, validator returns, blocks, decisions made alone, review findings, tests and lines; then the scored `retro-changes.csv` table. Quotable as it stands.
