```bash
git reset --soft HEAD~    # keep changes staged
git reset --mixed HEAD~   # keep changes unstaged (default)
git reset --hard HEAD~    # discard changes (dangerous)
```


```bash

echo a > one.txt
git add one.txt
git commit -m "first commit"
echo b > two.txt
git add two.txt
git commit -m "second commit"
git log --oneline
# shows two commits
git reset HEAD~    # now moves HEAD back one commit (undoes second commit)

```




**Short answer:** `git reset` moves the current branch pointer (HEAD) to another commit and optionally updates the *index* and *working tree*. Use `--soft` to keep changes staged, `--mixed` to keep them unstaged, and `--hard` to discard them — choose based on whether you want to preserve, rework, or throw away local changes.

### Git reset overview
**What it does:** `git reset <target>` changes where your branch points (HEAD). The three modes control two other areas: the **index** (staging area) and the **working tree** (files on disk). Visualize three layers: **HEAD (commit history)**, **index (what will be committed)**, and **worktree (your edits)**. `git reset` can move HEAD and then optionally update index and/or worktree to match that commit.

---

### Modes explained with concrete examples
#### 1. `--soft` — move HEAD only
- **Command:** `git reset --soft HEAD~1`  
- **Effect:** HEAD moves back one commit; **index and worktree are unchanged**. The changes that were in the undone commit remain staged, ready to be edited and recommitted.  
- **When to use:** You want to *reword or split* the last commit but keep its content staged for a new commit.  
- **Example:** You committed but forgot to include a file or want to change the commit message. Run `--soft`, add the missing file, then `git commit -m "new message"`.

#### 2. `--mixed` (default) — move HEAD and reset index
- **Command:** `git reset --mixed HEAD~1` or `git reset HEAD~1`  
- **Effect:** HEAD moves back; **index is reset to that commit**, but **worktree keeps your edits** as unstaged changes.  
- **When to use:** You want to undo a commit but keep the changes so you can rework them before staging again.  
- **Example:** You made a commit with multiple unrelated changes. Use `--mixed` to uncommit them, edit files, then selectively `git add` and create cleaner commits.

#### 3. `--hard` — move HEAD and overwrite index and worktree
- **Command:** `git reset --hard HEAD~1`  
- **Effect:** HEAD, index, and worktree all become the target commit. **All uncommitted changes are lost**.  
- **When to use:** You want to discard local changes and make your working copy exactly match a previous commit (e.g., to recover a clean state).  
- **Warning:** This is destructive; use only when you are sure you don’t need the uncommitted work.

---

### Practical workflows and safety tips
- **Recoverable workflow:** If unsure, `git reset --soft` or `--mixed` is safer than `--hard`. You can always `git commit` or `git stash` before destructive commands.  
- **Split a commit:** `git reset --mixed HEAD~1`, then `git add -p` to interactively stage hunks and create multiple commits.  
- **Undo a pushed commit:** Don’t use `reset` on commits already pushed to shared branches without coordination; prefer `git revert` to create a new commit that undoes changes.  
- **If you accidentally `--hard`:** check `git reflog` to find the lost commit and `git reset` back to it if it still exists in reflog.

---

**Bottom line:** pick the reset mode by how much you want to preserve: **soft = keep staged**, **mixed = keep unstaged**, **hard = discard everything**. Practice these in a throwaway repo to see HEAD/index/worktree behavior before using them on important branches. 
