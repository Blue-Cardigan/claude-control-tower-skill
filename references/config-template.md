# Control tower config

Copy to `.claude/control-tower.local.md` in the repository where the tower runs (or into your notes folder) and fill in each value. The tower reads this file at the start of every session. Keep it out of git if it names private systems.

## Repository

- **Trunk (`<TRUNK>`):** `main`
- **Integration command (`<INTEGRATE_CMD>`):** `gh pr merge --squash <branch>`
  - Run once per branch, never in a retry loop.
  - Serialised by: `<merge lock, merge queue, or "nothing yet">`

## Deploys

| Target (`<DEPLOY_TARGETS>`) | Command | Needs your explicit go? (`<APPROVAL_TARGETS>`) |
|---|---|---|
| staging | `<command>` | no |
| production | `<command>` | yes |

Other actions that need your explicit go in the acting session: `<for example: spend over a set amount, database migrations, production data writes>`

## Notes and register

- **Notes folder (`<NOTES_DIR>`):** `~/session-notes/<project>/`
- **Decisions register:** `<NOTES_DIR>/decisions-awaiting-owner.md`

## Client-data boundary (`<CLIENT_DATA_RULES>`)

`none`

Or, for each client or third-party dataset:
- **Where it may live:** `<private storage only>`
- **Where it must never go:** `<git, docs, agent memory, shared models>`
- **Who may read it:** `<named sessions or roles>`
- **Who approves new people:** `<you, or a named person>`

## Machine

- **Heavy-job runner (`<HEAVY_JOB_CMD>`):** `<command, or "none">`
- **Priority when memory is tight (highest first):** external deadlines, scheduled recurring jobs, validation runs, exploratory runs
- **Remote runner for long or scheduled compute:** `<service, or "none">`

## Cadence

- **Sweep (`<CADENCE>`):** every 3–4 hours, and whenever more than about 20 sessions are open
