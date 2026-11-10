# Sweep checklist

Run through this list for each sweep. The `<…>` values come from your control tower config file (see "Settings" in `SKILL.md`).

## 1. List

- [ ] List all open sessions (desktop app: the session-listing tool; terminal: your own list of running sessions or status files in `<NOTES_DIR>`).
- [ ] Skip sessions that are running, pinned, or titled "Do not archive".

## 2. Read

- [ ] For each idle session, read its last assistant message only.
- [ ] If the last message is just a tool result, page back one or two turns.

## 3. Classify

| Class | Test | Action |
|---|---|---|
| Finished | Its work is on `<TRUNK>` or live, or its study ended under its own rules | Move open decisions to the register, branch-check, archive |
| Lane-ready | Finished, branch pushed, not yet on `<TRUNK>` | Run `<INTEGRATE_CMD> <branch>` once; on success, branch-check and archive |
| Stalled | Last turn ended on an API, network or job error | Send a resume message naming what failed |
| Unblockable | Waits on something the tower has | Send it, and say when it applies |
| Waiting on you | Needs your decision | Keep; add to the register; file under "Awaiting you" |
| Area owner | Coordinates an active area | Keep |
| Live dependency | Something live may still need it | Keep until that ends |

## 4. Branch check (before every archive)

For each worktree the session owns:

```bash
git fetch origin
git rev-list --count origin/<TRUNK>..<branch>   # 0 means everything is on trunk
git rev-parse --abbrev-ref <branch>@{upstream}  # errors if the branch was never pushed
git -C <worktree> status --porcelain --untracked-files=no   # empty means a clean tracked tree
git -C <worktree> status --porcelain --ignored  # look for unique ignored results or logs
```

- [ ] Commits not on trunk: push the branch, or integrate it, before archiving.
- [ ] Tracked changes: ask the session to commit or explain them. Do not archive.
- [ ] Unique ignored files (results, evidence, logs): copy them somewhere safe and verify the copy (for example, compare checksums).
- [ ] Parent session: repeat for each finished child, since archiving the parent archives them too.

## 5. Report

- [ ] Archived: name and one-line reason.
- [ ] Integrated: branch and trunk commit.
- [ ] Resumed and unblocked: what was sent.
- [ ] Kept: one-line reason each.
- [ ] Stale worktrees left on disk (branch fully on trunk, clean, no open session).
- [ ] Register updated.
- [ ] Next sweep scheduled.
