

**Git stash temporarily shelves your uncommitted changes so you can switch context without committing; use `git stash` to save work, `git stash list` to view, `git stash apply` to reapply without removing, and `git stash pop` to reapply and remove the stash**.

### Overview
**What stash does:** it saves the current *working tree* and (optionally) *untracked* files into a stack-like storage so your branch becomes clean and you can switch commits or branches safely. Stashes do not create commits on your branch history; they are separate entries you can reapply later.

### When to use stash
- **Interruptions**: you need to switch branches to fix a bug or pull updates but your work is incomplete.  
- **Experimenting**: try something on a clean tree without losing current edits.  
- **Partial commits**: you want to keep WIP out of history until it’s ready.  
These use cases are common and recommended in workflows that avoid committing half-done work.

### Basic commands and step‑by‑step examples
1. **Save changes**
```bash
git stash push -m "WIP: feature X"
```
This saves tracked changes (and staged changes) and restores a clean working tree. To include untracked files use `-u` or `--all` to include ignored files as well.

2. **List stashes**
```bash
git stash list
```
Shows entries like `stash@{0}: WIP on master: ...` so you can pick which to reapply.

3. **Inspect a stash**
```bash
git stash show stash@{0}
git stash show -p stash@{0}   # show full diff
```

4. **Reapply without removing**
```bash
git stash apply stash@{0}
```
Use this when you want to keep the stash for reuse or multiple branches.

5. **Reapply and remove**
```bash
git stash pop stash@{0}
```
This reapplies the stash and deletes it from the stash list; if conflicts occur you must resolve them like a merge.

### Advanced usage and tips
- **Stash only staged or patch mode**: `git stash push -k` keeps index, `-p` lets you interactively choose hunks to stash.  
- **Create a branch from a stash**: `git stash branch my-branch stash@{0}` creates a new branch and applies the stash there — great for turning WIP into a proper feature branch.  
- **Drop or clear**: `git stash drop stash@{0}` removes one entry; `git stash clear` empties all stashes. Use carefully — stashes can be lost if dropped.  
- **Stash conflicts**: applying a stash can produce conflicts; resolve them and `git add` then `git commit` as usual.

### Recovery and best practices
- **Name stashes** with `-m` so `git stash list` is readable.  
- **Prefer `apply`** if you want to keep a copy; use `pop` when you’re done with that stash.  
- **If you accidentally drop** a stash, check `git fsck --lost-found` or reflog-like entries; recovery is possible but harder.  
- **Don’t use stash as long-term storage** — it’s for short-term context switches, not permanent backups.

---

