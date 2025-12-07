
**Short answer:** **`git revert` creates a new commit that undoes the changes introduced by a previous commit without rewriting history**, so it’s safe for public branches; **`git reset` moves the branch pointer and can rewrite history or discard changes** depending on mode.

### What `git revert` does
`git revert <commit>` computes the inverse of the specified commit and makes a **new commit** that applies that inverse change. The original commit remains in history; the revert is an explicit “undo” recorded as a new commit. Use this when you need to undo something on a branch that others may already have pulled.

### Simple step‑by‑step example
1. Create two commits:
```bash
echo A > file.txt
git add file.txt
git commit -m "Add A"
echo B >> file.txt
git add file.txt
git commit -m "Add B"
```
2. Inspect history:
```bash
git log --oneline
# shows: <hashB> Add B
#        <hashA> Add A
```
3. Revert the second commit:
```bash
git revert <hashB>
# opens editor to edit revert message; save and close
```
4. Result: history now has a new commit that removes the changes introduced by `Add B` while keeping `Add B` visible in history.

### Reverting multiple commits
- Revert commits one by one in reverse chronological order (newest first) to avoid conflicts.  
- You can also create a single revert commit for a range by reverting each commit in sequence or by using `git revert --no-commit <hash1> <hash2> ...` then committing once.

### Reverting a merge commit
Reverting a merge is special because Git must know which parent to invert. Use:
```bash
git revert -m 1 <merge-hash>
```
`-m 1` tells Git to treat parent 1 as the mainline; choose the parent that represents the branch you want to keep.

---

### `git revert` versus `git reset` — core differences
- **History**: `git revert` **preserves history** by adding a new commit that undoes changes; `git reset` **moves the branch pointer** and can remove commits from the branch history (rewriting it).  
- **Safety on shared branches**: `git revert` is safe on public branches; `git reset` (especially `--hard`) is dangerous if commits were pushed because it requires force-pushing and can confuse collaborators.  
- **Effect on working tree and index**: `git reset --soft` keeps changes staged; `--mixed` keeps them unstaged; `--hard` discards them. `git revert` never touches other commits and only creates a new commit that reverses a specific commit’s changes.

#### Example that contrasts them
- You pushed commit `C` and want to undo it for everyone: run `git revert C` — this creates `C'` that undoes `C` and can be pushed safely.  
- If `C` is local and you want to remove it from history entirely: `git reset --hard HEAD~1` will drop `C` locally, but if `C` was pushed, you’d need `git push --force` which can break others’ clones.

---

### Practical guidance and best practices
- **Use `git revert` on shared branches** (main, master, release) to undo changes without rewriting history.  
- **Use `git reset` for local cleanup** (amending, splitting commits) before pushing; prefer `--soft` or `--mixed` when you want to preserve work.  
- **When in doubt**: prefer `revert` for public history and `reset` for local, private history edits.

These patterns will help you choose the right tool: **`revert` = safe undo recorded in history; `reset` = move/erase history locally**.



---


### Reverting a merge commit explained
Reverting a merge is special because Git must know which parent to invert. Use:
```bash
git revert -m 1 <merge-hash>
```

**Short answer:** Reverting a merge requires telling Git *which parent* of the merge should be treated as the “mainline” so Git can compute the inverse change; use `git revert -m 1 <merge-hash>` to keep parent 1 and undo the merged-in branch, or `-m 2` to keep parent 2.

### Explanation of parents in a merge
A merge commit has **two (or more) parents**: parent 1 is the commit you were on when you ran `git merge`, and parent 2 is the tip of the branch you merged in. Reverting a normal (non-merge) commit is straightforward because Git can invert the single change. For a merge, Git must know *which side* of the merge should be considered the baseline (the “mainline”) so it can compute the inverse diff correctly.

### Visual example to make it concrete
1. Create two branches:
```bash
git checkout -b main
echo A > file.txt
git add file.txt
git commit -m "A on main"   # commit M1 (parent 1)
git checkout -b feature
echo B >> file.txt
git commit -am "B on feature" # commit F1 (parent of feature)
git checkout main
git merge feature             # creates merge commit M2 with parents (main=M1, feature=F1)
```
Here the merge commit M2 has **parent1 = M1** (main) and **parent2 = F1** (feature). If you want to undo the effect of merging *feature* while keeping main as it was, you revert M2 using `-m 1` so Git treats parent1 as the mainline and removes the changes introduced by parent2.

### Step‑by‑step revert of the merge
```bash
# find the merge commit hash
git log --oneline --graph

# revert the merge, keeping parent 1 (main)
git revert -m 1 <merge-hash>
git commit -m "Revert merge of feature into main"
```
This creates a new commit that undoes the merge’s net changes without deleting the original merge commit from history — safe for shared branches.

### Choosing -m 1 or -m 2
- Use **`-m 1`** when you want to *keep the branch you were on* (parent 1) and undo the merged-in branch.  
- Use **`-m 2`** when you want to *keep the merged-in branch* and undo the branch you were on at merge time.  
Pick the parent that represents the state you want to preserve; inspect the merge with `git show <merge-hash>` to confirm which parent is which.

### Conflicts and tips
Reverting a merge can produce conflicts because the inverse change may not apply cleanly; resolve conflicts, `git add` the resolved files, and finish the revert commit. If the merge was complex or you need to undo multiple merges, consider creating a branch to test the revert first or use `git revert --no-commit` to batch multiple reverts before committing.
