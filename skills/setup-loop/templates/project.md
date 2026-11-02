# How this project runs the loop

Every agent reads this file first: `## Settings`, `## For every agent`, and its own section. The retro writes lessons into the role sections, one imperative sentence each; the stories behind them go to `history/lessons.md`. Keep each role section under the word budget; the retro consolidates it.

## Settings

| Setting | Value |
|---|---|
| Main branch | `main` |
| Feature branch prefix | `feature/` |
| Verify gate | `<command that must pass, e.g. npm run typecheck && npm run lint && npm run test && npm run build>` |
| Integration tests | `<command and what it needs, or "none">` |
| Engineering standards | `CLAUDE.md` |
| Decisions log | `DECISIONS.md` (short; older entries searched, not read) |
| Feature docs | `<file that says what the product does today, or "none">` |
| Has a UI | `<yes / no>` |
| Widths checked | `390, 1280` |
| Design folder | `docs/design/` |
| Owner's own screens | `<folder, or "none">` |
| UX standards | `<file, or "none">` |
| Claim timeout | 40 minutes |
| Heartbeat | 30 minutes |
| Validator revisions | 2 |
| Review rounds | 3 |
| Word budget per role section | 400 |
| Owner's time zone | `<e.g. Asia/Kolkata>` |
| Model overrides | `<role: model, or "none">` |

## Routines

| Routine | Id | Schedule | State |
|---|---|---|---|
| Loop (runs `run-loop`) | `<id>` | `<cron, UTC>` | `<enabled / paused>` |
| Check-in (runs `check-in`) | `<id>` | `<cron, UTC>` | `<enabled / paused>` |

The check-in reconciles this table with the live routines on every check-in.

## Data

<Where real data lives that specs and tests must be grounded in (a
snapshot folder, a public source to fetch from and how), and who may
refresh it. What may never be copied into the repo, and why.>

## Deployment

<What production actually runs: the host, how files are shipped, time
limits, scheduled jobs, how to check the deployed shape locally.>

## Health check

<What the retro checks on the live product after every feature: the
read-only access (an environment variable naming it), the queries or
checks, what counts as a defect, and what it may never write down. Or
"none yet".>

## For every agent

<Project rules every role follows. Start with the rules that would hurt
most to break: secrets, private data, the one way to do each thing.>

## manager

## pm-author

## ui-designer

## pm-validator

## engineer

## code-reviewer
