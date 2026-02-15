**`git revert`**, how it works, how it differs from `git reset`, and why it’s safer in shared repositories.

---

## 🔹 What `git revert` Does
- `git revert` **creates a new commit** that undoes the changes introduced by a previous commit.
- It does **not remove history** — instead, it adds a new commit on top.
- This makes it safe for collaboration because the commit history remains intact.

---

## 🔹 Example Walkthrough

### Step 1: Create a few commits
```bash
echo "A" > file.txt
git add .
git commit -m "Commit 1: Add A"

echo "B" >> file.txt
git add .
git commit -m "Commit 2: Add B"

echo "C" >> file.txt
git add .
git commit -m "Commit 3: Add C"
```

History:
```
Commit 3: Add C   (HEAD -> main)
Commit 2: Add B
Commit 1: Add A
```

### Step 2: Revert Commit 2
```bash
git revert <commit-id-of-Commit-2>
```

This creates a new commit:
```
Commit 4: Revert "Commit 2: Add B"
Commit 3: Add C
Commit 2: Add B
Commit 1: Add A
```

- The file now contains `"A\nC"` (because the `"B"` addition was undone).
- Notice Commit 2 is still in the log — history is preserved.

---

## 🔹 Difference Between `git reset` and `git revert`

| Feature              | `git reset`                        | `git revert`                          |
|----------------------|-------------------------------------|---------------------------------------|
| Effect on history    | Moves HEAD back, **removes commits from branch history** | Adds a new commit that undoes changes |
| Visibility in `git log` | Commits disappear from log (unless rescued via reflog) | Commits remain visible, plus a new “revert” commit |
| Collaboration impact | Dangerous in shared repos (rewrites history) | Safe in shared repos (preserves history) |

---

## 🔹 Why `git revert` Is Safer in Shared Repos
- In a shared repo, others may have already pulled the commits you want to undo.
- If you use `git reset` and push, you rewrite history → teammates’ histories diverge → conflicts.
- `git revert` avoids this by **adding a new commit** instead of deleting old ones.
- Everyone’s history stays consistent, and the unwanted changes are undone cleanly.

---

## ✅ Key Takeaways
- Use **`git reset`** for local cleanup (before pushing).
- Use **`git revert`** when collaborating — it’s the safe way to undo changes without breaking history.
- `git log` after `reset` hides commits; after `revert`, it shows everything plus the new revert commit.

