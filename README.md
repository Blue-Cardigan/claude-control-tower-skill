# Control tower skill (general version)

A Claude Code skill for people who run many Claude Code sessions at once, in the desktop app or in several terminals, and want one long-lived session to coordinate them. The tower gives check-ins, keeps a register of decisions waiting on you, routes work, sweeps idle sessions, recycles long ones, and keeps merges, deploys and heavy jobs from colliding.

## Install

```bash
mkdir -p ~/.claude/skills/control-tower-general
cp -R SKILL.md references ~/.claude/skills/control-tower-general/
```

Run the copy from the root of this repository.

## Initialise

Open Claude Code in the repository you want coordinated and say **"set up control tower"**. The tower will:

1. look at the repository (default branch, how changes merge, deploy scripts, CI, existing `CLAUDE.md`) and propose its settings for you to confirm;
2. write them to `.claude/control-tower.local.md`, so the skill itself stays unchanged and easy to update;
3. create your notes folder and an empty decisions register;
4. offer a short block for your `CLAUDE.md` so every other session cooperates with the tower (no relayed approvals, one worktree per task, clear hand-over notes); it only edits `CLAUDE.md` if you say yes;
5. point out missing safeguards, such as no lock around merges or no single deploy command per target;
6. run a first check-in.

You can also fill in [`references/config-template.md`](references/config-template.md) by hand and save it as `.claude/control-tower.local.md`.

## Use

After setup, keep that session as your tower: ask it for a check-in, a sweep or a decisions digest. Pin it if your app allows it.

## Files

| File | What it is |
|---|---|
| `SKILL.md` | The skill: settings, first-run setup, standing rules, routines and pitfalls |
| `references/config-template.md` | Template for your settings file |
| `references/claude-md-snippet.md` | Optional rules for your `CLAUDE.md` so other sessions cooperate |
| `references/sweep-checklist.md` | Step-by-step sweep, including the branch check before archiving |
| `references/decisions-register-template.md` | Template for the decisions register kept in your notes folder |

## Where it works best

The desktop app has tools to list, message, archive and group sessions, and the skill uses them where available. In a plain terminal setup, each section says what to do instead, usually a status file per session in a shared notes folder.

## Licence

MIT. See [`LICENSE`](LICENSE).
