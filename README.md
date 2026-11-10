# Control tower skill (general version)

A Claude Code skill for people who run many Claude Code sessions at once, in the desktop app or in several terminals, and want one long-lived session to coordinate them. The tower gives check-ins, keeps a register of decisions waiting on you, routes work, sweeps idle sessions, recycles long ones, and keeps merges, deploys and heavy jobs from colliding.

## Install

```bash
mkdir -p ~/.claude/skills/control-tower-general
cp -R SKILL.md references ~/.claude/skills/control-tower-general/
```

Run the copy from the root of this repository. Then open `~/.claude/skills/control-tower-general/SKILL.md` and fill in the "Configure for your setup" table: your trunk branch, integration command, deploy targets and which need your explicit approval, your shared notes folder, your client-data rules (or "none"), and your heavy-job runner.

## Use

Start a session and say "be my control tower", then ask for a check-in, a sweep or a decisions digest. Pin that session if your app allows it.

## Files

| File | What it is |
|---|---|
| `SKILL.md` | The skill: configuration, standing rules, routines and pitfalls |
| `references/sweep-checklist.md` | Step-by-step sweep, including the branch check before archiving |
| `references/decisions-register-template.md` | Template for the decisions register kept in your notes folder |

## Where it works best

The desktop app has tools to list, message, archive and group sessions, and the skill uses them where available. In a plain terminal setup, each section says what to do instead, usually a status file per session in a shared notes folder.

## Licence

MIT. See [`LICENSE`](LICENSE).
