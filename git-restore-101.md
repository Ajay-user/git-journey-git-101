**Git `restore` reverts files in the working tree and/or the index to a chosen source. Use `git restore <path>` to discard unstaged edits, `git restore --staged <path>` to unstage, and `git restore .` to restore everything in the current directory.**

### git restore overview
**`git restore`** is a focused command introduced to make file-level restores clearer than older commands like `git checkout` or `git reset`. By default it restores the working tree from the index; with `--staged` it restores the index (staging area) from `HEAD` unless you specify a different `--source`. Use it when you want to discard or move changes between working tree and index safely.

### Common usages with examples
#### Discard unstaged changes in a single file
```bash
git restore file.txt
```
This replaces `file.txt` in your working tree with the version currently staged in the index (or with `HEAD` if nothing is staged), effectively **discarding your uncommitted edits** to that file.

#### Discard all unstaged changes in the current directory
```bash
git restore .
```
This restores every tracked file under the current directory to the staged/index state (or `HEAD` if nothing is staged), so it’s a quick way to undo many local edits at once.

#### Unstage a file (move from index back to working tree)
```bash
git restore --staged file.txt
```
This removes `file.txt` from the staging area so it will not be included in the next commit, while leaving your working copy unchanged. It’s the modern alternative to `git reset HEAD <file>` and is recommended by newer Git versions and tutorials.

#### Restore both index and working tree from a specific commit
```bash
git restore --source=HEAD~1 --staged --worktree file.txt
```
This sets both the staged version and the working copy of `file.txt` to the version from `HEAD~1` (one commit back) — useful when you want to roll a file back to a previous commit and stage that rollback for the next commit.

### How `--staged` and `--worktree` choose their source
- **Without `--staged`**: `git restore <path>` restores the working tree from the index.  
- **With `--staged` only**: it restores the index from `HEAD` by default.  
- **With `--source=<tree>`**: you can pick any commit/tree to restore from (not just `HEAD`).

### Safety tips and workflows
- **If you want to keep changes before switching commits**, use `git stash` instead of `git restore`.  
- **To stop tracking a file but keep it locally**, use `git rm --cached <file>` and add it to `.gitignore` (don’t use `git restore` for that).  
- **When unsure, run `git status` first** to see what’s staged vs unstaged; `git restore` is destructive for uncommitted edits, so double-check before running it.

These examples should help you practice: try making edits, staging some, and then using `git restore` with and without `--staged` to see how the index and working tree change.


---

**Short answer:** This command **resets both the index (staging area) and your working copy of `file.txt` to the version from the previous commit (`HEAD~1`)**, effectively rolling that file back one commit and staging that rollback for the next commit.

### What the pieces mean
- **`--source=HEAD~1`** picks the tree (the version of files) from one commit before HEAD (the parent commit).  
- **`--staged`** updates the **index** so the chosen version is staged for commit.  
- **`--worktree`** updates the **working copy** on disk to match the chosen version.  
Together they make both the staged snapshot and your file on disk match `HEAD~1` for `file.txt`.

---

### Why you’d use this
Use this when you want to *revert a single file* to how it looked in the previous commit and have that revert ready to commit immediately — without touching other files or doing a full `git revert` or `git reset --hard`.

---

### Step‑by‑step examples

#### Example 1 — You edited a file and want to go back one commit for that file only
```bash
# Current history: commit-A (HEAD), commit-B (HEAD~1)
# You changed file.txt in working tree and maybe staged it.

git restore --source=HEAD~1 --staged --worktree file.txt
# Now file.txt on disk and in the index equals the version from commit-B (HEAD~1).
git status
# Shows file.txt as staged for commit (the rollback).
git commit -m "Revert file.txt to version from HEAD~1"
```
Result: only `file.txt` is reverted and that revert is recorded as a new commit.

#### Example 2 — You want to test an older version without committing yet
```bash
git restore --source=HEAD~1 --worktree file.txt
# Only changes working copy; index unchanged.
# You can inspect or run tests using the older file.
git restore --staged file.txt   # to unstage if needed
```
This lets you try the older file without staging it.

#### Example 3 — Undo a mistaken staged change and working edits
```bash
# You staged and modified file.txt, but want to discard both and use HEAD~1
git restore --source=HEAD~1 --staged --worktree file.txt
```
This replaces both index and working copy with the older version so nothing from your edits remains.

---

### Important notes and safety
- **This is destructive for uncommitted edits**: if you had unsaved changes in `file.txt`, they will be replaced. Stash or commit them first if you want to keep them.  
- `HEAD~1` refers to the parent commit; if you need a different commit use its SHA or another ref.  
- `git restore` is the modern, file-focused replacement for older patterns like `git checkout -- <file>` and `git reset HEAD <file>`; it’s clearer about whether you affect the index, the worktree, or both.

---

**Quick practice tip:** Try these commands in a throwaway repo: make two commits changing `file.txt`, then run the examples above to see how index and working tree change. This hands‑on view makes the three Git areas (HEAD, index, worktree) click. 




