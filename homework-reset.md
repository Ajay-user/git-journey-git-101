## 🔹 1. `git reset`
This moves the **HEAD pointer** (your current branch reference) to a different commit, but **does not delete changes in your working directory**. By default, it’s a **soft reset**.

### Example:
```bash
touch file-a.txt
echo "A" > file-a.txt
git add .
git commit -m "Adding file-a"

echo "B" > file-a.txt
git add .
git commit -m "Update file-a with B"
```

Now your history looks like:
```
Commit 2: Update file-a with B
Commit 1: Adding file-a
```

If you run:
```bash
git reset HEAD~1
```
- HEAD moves back to **Commit 1**.
- The changes from Commit 2 (the "B" update) are **unstaged but still in your working directory**.

So `file-a.txt` will still contain `"B"`, but Git thinks you haven’t committed it yet.

---

## 🔹 2. `git reset --hard`
This is the **dangerous one**. It moves HEAD back and **erases changes in your working directory** to match the commit you reset to.

### Example:
Using the same history:
```
Commit 2: Update file-a with B
Commit 1: Adding file-a
```

Run:
```bash
git reset --hard HEAD~1
```
- HEAD moves back to **Commit 1**.
- The working directory is reset too → `file-a.txt` now contains `"A"`.
- The `"B"` change is **gone** (unless you had it saved elsewhere).

---

## 🔹 3. `git reset HEAD~`
This is shorthand for `git reset HEAD~1`.  
It means “reset to the commit one before HEAD.”

### Example:
```bash
git reset HEAD~
```
- Same as `git reset HEAD~1`.
- HEAD moves back one commit.
- Changes from the last commit are unstaged but still present in your files.

---

## ✅ When to Use Each
- **`git reset` (soft)** → When you want to undo a commit but keep the changes staged/unstaged for editing or recommitting.
- **`git reset --hard`** → When you want to completely discard commits and changes (use with caution).
- **`git reset HEAD~`** → A quick way to undo the last commit, keeping changes in your working directory.

---

## Quick Comparison Table

| Command              | Moves HEAD | Keeps changes in files | Keeps changes staged |
|----------------------|------------|------------------------|----------------------|
| `git reset` (soft)   | ✅         | ✅                      | ✅                  |
| `git reset HEAD~`    | ✅ (1 back)| ✅                      | ❌ (unstaged)        |
| `git reset --hard`   | ✅         | ❌ (discarded)          | ❌                  |

---
