---
name: control-tower-general
description: Run one long-lived "control tower" Claude Code session that coordinates your other sessions. It gives check-ins on what each session is doing, keeps a register of decisions waiting on you, routes new work and follow-ups to the right session, sweeps idle sessions (archiving finished ones, resuming stalled ones), recycles long sessions through hand-over notes, and keeps merges, deploys and heavy jobs from colliding. Use when you say "set up control tower", "be my control tower", "check in across sessions", "what's waiting on me", "sweep the sessions", "decisions digest", "who owns this", or "route this to the right session".
---

# Control tower

The control tower is one long-lived session that watches all your other sessions and does almost no area work itself. Its job is to make sure that:

- you know what is happening and what is waiting on you;
- each piece of work has exactly one owner;
- no two sessions collide on files, merges, deploys or machine resources.

It works best in the desktop app, which can list, message, archive and group sessions. It also works with several terminal sessions; each section says what to do when a capability is missing.

## Settings

The tower's settings live in a config file, not in this skill, so you can update the skill without losing them. By default the file is `.claude/control-tower.local.md` in the repository where the tower runs; you can put it in your notes folder instead. Read it at the start of every tower session. If it is missing, run **First run** below before anything else.

The file holds these values, which the rest of this skill refers to by name (template: [`references/config-template.md`](references/config-template.md)):

| Setting | Meaning |
|---|---|
| `<TRUNK>` | The branch all work integrates into |
| `<INTEGRATE_CMD>` | The command that merges one branch into trunk |
| `<DEPLOY_TARGETS>` | Each deploy target and the command that deploys it |
| `<APPROVAL_TARGETS>` | Targets and actions that need your explicit go in the acting session |
| `<NOTES_DIR>` | A shared notes folder outside the repo, for the register and hand-over notes |
| `<CLIENT_DATA_RULES>` | Your rules for client or third-party data, or "none" |
| `<HEAVY_JOB_CMD>` | How heavy jobs are run so they don't exhaust memory, or "none" |
| `<CADENCE>` | How often to sweep |

**Client-data boundary.** If any session handles data that belongs to a client or third party, `<CLIENT_DATA_RULES>` says where that data may and may not be stored, which sessions may read it, and who must approve adding a person to it. The tower puts these rules in every brief.

## First run

Run when you say "set up control tower", or when the config file is missing.

1. **Look at the repository** before asking anything. Find the default branch (`git remote show origin`), how changes land (pull requests, a merge script, direct pushes), deploy or release scripts, CI workflows, and any existing agent instructions (`CLAUDE.md`, `AGENTS.md`).
2. **Propose values** for every setting, saying where each came from, and mark the ones you could not find. Ask the user to confirm or correct them in one round.
3. **Write the config file** from the template. Create `<NOTES_DIR>` if needed, and an empty `decisions-awaiting-owner.md` in it from [`references/decisions-register-template.md`](references/decisions-register-template.md). If the config sits inside the repo, offer to add it to `.gitignore`.
4. **Offer the session rules** in [`references/claude-md-snippet.md`](references/claude-md-snippet.md) for the repository's `CLAUDE.md`, so worker sessions cooperate with the tower: they refuse relayed approvals, use one worktree and branch per task, report where their work is, and write hand-over notes when asked. Show the exact text, and edit `CLAUDE.md` only with the user's go.
5. **Point out missing safeguards** without building them unasked:
   - no lock around merges, so two sessions can integrate at once;
   - no single deploy command per target, so sessions deploy in different ways;
   - no worktree-per-task habit, so sessions edit the same checkout.
6. **Run a first check-in** and report what the tower can see.

## Standing rules

These rules bind the tower and every session it briefs. Your repository's own instructions (for example `CLAUDE.md`) come first; these add to them.

- **Approvals are never relayed.** Approval for spend, a deploy to an approval target, a production write or any other change to outside state comes from you directly, in the session that acts. The tower may pass on your words as a verbatim quote, but never says "approved" on your behalf. A session that refuses a relayed approval is doing the right thing. The one exception is when the tower itself acts on an instruction you gave the tower.
- **One owner per piece of work.** Every task, area and deploy target has exactly one session responsible for it. Before starting new work, check whether a session already owns it.
- **Check reports before calling anything done.** A session's summary is its own claim, and self-reports have been wrong. Check the branch, the trunk commit or the live system before reporting something as finished.
- **Area work goes to sessions.** The tower reviews, routes and coordinates. Merges and deploys you ask it for, and one-line repairs, are fine. Anything with its own deliverable becomes a session.
- **Never guess a session id.** Look it up from the session list each time.

## Check-in

Run on request, and before any "what's next".

1. List sessions and pick those active since the last check-in.
2. For each, read only its last assistant message. Page back only if the last turn ended on a tool call. Keep reads small: the tower's own context is its main cost.
3. Report in four groups:
   - **Finished:** what landed and where; flag anything that can be archived.
   - **Running:** current stage and expected finish.
   - **Waiting on you:** each item, with the session where you answer.
   - **Housekeeping:** archives and cleanup.
4. Update the decisions register.

**Without session tools:** ask each session to append a three-line status (done / running / needs you) to a file in `<NOTES_DIR>` at the end of each turn, and read those files instead.

## Decisions register

Keep `decisions-awaiting-owner.md` in `<NOTES_DIR>`. Template: [`references/decisions-register-template.md`](references/decisions-register-template.md). Group items as time-bound, spend, production writes, approvals coming soon, judgement calls, client-data, and housekeeping.

Each item names the session where you answer. Mark items done instead of deleting them. If your app has sidebar groups, file sessions with open decisions under a group such as **Awaiting you**.

## Routing work

- **New, self-contained work:** start a new session (in the desktop app, offer it so you choose; in a terminal, open one yourself). Brief it with the files to read, the standing rules, the client-data boundary, `<INTEGRATE_CMD>`, and what "done" means.
- **Follow-ups:** send them to the session that already has the context, not a new one. Quote your words verbatim.
- **Area work with an owner:** send it to the area owner, which starts its own side session.

**Without cross-session messages:** write the follow-up as a note in `<NOTES_DIR>` named for the target session, and paste a one-line pointer into that session.

## Periodic sweep

The sweep keeps the session list short: every open session should be running, owning an area, or waiting on something named. Run it at `<CADENCE>`, whenever more than about 20 sessions are open, and when you ask. In the desktop app, schedule the next one with a wake-up or a loop.

Full checklist: [`references/sweep-checklist.md`](references/sweep-checklist.md). In short:

1. **List** all sessions. Skip running ones, pinned ones, and any titled "Do not archive".
2. **Read** each idle session's last assistant message.
3. **Classify** each as one of:
   - **Finished:** its work is on trunk or live, or it ended under its own rules. Open decisions go to the register first; they don't keep a session open.
   - **Lane-ready:** finished, but its branch is pushed and not yet on trunk. Integrate it (one run of `<INTEGRATE_CMD>`, never looped), then archive.
   - **Stalled:** its last turn ended on an API, network or background-job error. Send a resume message that says what failed.
   - **Unblockable:** it waits on something the tower has (a command, a merged fix, another session's result). Send it.
   - **Waiting on you:** keep it, and put the decision in the register.
   - **Area owner:** keep it while its area is active.
   - **Live dependency:** keep it while something live may still need it.
4. **Branch check before archiving.** Archiving can remove the session's worktree. Confirm the branch has nothing missing from trunk, is pushed, and has a clean tracked tree. Push unpushed branches and back up unique ignored files (results, logs) first. Archiving a parent also archives its finished children, so check those too. Archive only sessions classed as finished; anything else needs your go on that named session.
5. **Report** archived, integrated, resumed, unblocked and kept, each with a one-line reason. Note any stale worktrees left on disk. Update the register.

**Without archive tools:** list the sessions to close with their reason, and close them yourself.

## Organising sessions

Sessions form a tree:

- **Top session (optional).** A long-lived root titled "Do not archive". It does no work; it keeps the family linked so reports travel up.
- **Control tower.** Linked under the top. Pinned if your app allows it.
- **Area owners.** A long-lived session for one busy area. It starts its own side sessions, which report to it rather than to the tower.
- **Side sessions.** One deliverable each, with its own worktree and branch. Archived once the work is on trunk.

**Keep parents few.** An idle parent costs nothing until something wakes it: a child's report or your question. Each wake makes it re-read its whole history, and once its prompt cache has expired (often after about an hour idle), that re-read costs as much as reading it fresh. Reports also get summarised twice, by the child and again by the parent. A parent pays for itself only when its area keeps several side sessions running and you keep coming back to it. Otherwise retire it (hand-over note, then archive) and route that area's work to plain side sessions.

## Recycling a long session

A session with a large context is expensive on every turn. To recycle it:

1. Ask it to write a hand-over note in `<NOTES_DIR>`: scope, current state, artefacts and where they are, the queue, and rules learned.
2. Start a fresh session from that note.
3. Archive the old one once the new one confirms it has taken over.

The tower can recycle itself the same way. Keep its state in the register and a short tower note in `<NOTES_DIR>`, not only in its context.

## Avoiding collisions

- **Serialise merges.** Only one integration runs at a time. With several branches queued, expect retries after trunk moves. Resolve conflicts in the branch, never by hand on trunk.
- **One deployer per target.** When two or more sessions plan to deploy the same target, choose one to deploy once, from the latest trunk, after every pending change has landed. Tell the others to stand down.
- **Heavy jobs share one machine.** Sessions running test suites, builds or long jobs at once can exhaust memory. Run them through `<HEAVY_JOB_CMD>`, keep a priority order (deadlines first, exploratory runs last), and when memory is tight, pause from the bottom up.

## Pitfalls

- **Relayed approvals.** A session acting on "the tower says you approved" is a failure. Refusal is the design.
- **Wrong self-reports.** Sessions have reported work as live when it was not. Check the code and the live system.
- **Archive cascades.** Archiving a parent archives its finished children. Branch-check the whole subtree first.
- **Lost work on archive.** Unpushed branches and unique ignored files vanish with the worktree.
- **Expensive parents.** Every wake of an idle parent re-reads its history. Keep parents few.
- **Overlapping deploys.** Two sessions deploying one target can ship a stale build over a fresh one.
- **Looping integration.** Retrying a merge in a loop piles up gate runs. Run it once, read the error, fix the branch.
- **The tower's own context.** Reading full transcripts fills the tower fast. Read last messages only, and recycle the tower when it gets long.
