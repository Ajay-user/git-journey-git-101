To unstage a file in Git after you've added it, you can use the `git reset` command. In your case, since you want to unstage **b.txt**, you would run:

```bash
git reset b.txt
```

This removes **b.txt** from the staging area but keeps the changes in your working directory. So the file is still modified, but it won’t be included in the next commit unless you add it again.

👉 Quick recap:

- `git add <file>` → stages the file.
- `git reset <file>` → unstages the file (but doesn’t discard changes).
- If you wanted to discard changes entirely, you’d use `git checkout -- <file>` (or `git restore <file>` in newer versions).
