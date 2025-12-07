

`git commit --amend` *replaces* your most recent commit with a new commit that can have a different message and/or different content; it **rewrites local history** and changes the commit hash, so avoid amending commits that you've already pushed to a shared branch without coordination.

### What `--amend` actually does
`git commit --amend` takes whatever is currently staged and makes a **new commit object** that replaces the previous HEAD commit. The old commit still exists until garbage-collected, but your branch now points to the new commit — this is why we say it *rewrites history* (it’s safe locally but can confuse collaborators if pushed without care).

### Concrete, step‑by‑step example
Imagine you made a commit but forgot to add `missed.txt` and want to fix the commit message too.

1. You check status and see `missed.txt` is untracked:
```bash
git status
# On branch feature 
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git restore <file>..." to discard changes in working directory)
#       new file:   missed.txt
```

2. Stage the missed file:
```bash
git add missed.txt
```

3. Amend the last commit and change the message:
```bash
git commit --amend -m "Add missed.txt and clarify behavior"
```

**Result:** The last commit now includes `missed.txt` and has the new message. The commit hash is different because the commit content (and/or message) changed.

### How to see what changed
Run:
```bash
git log --oneline -n 2
```
You’ll see the previous commit replaced by a new one (different short hash). If you want to inspect the difference between the amended commit and the previous one, use the reflog or compare hashes before amending:
```bash
git reflog
git show <old-hash>
```

### Important cautions and remote workflow
- **If you have not pushed** the original commit, amending is simple and safe.  
- **If you already pushed** the original commit to a remote, amending creates a different commit history; pushing requires a force push (`git push --force` or `git push --force-with-lease`) and can disrupt others who pulled the original commit. Use force-with-lease to reduce risk.  
- Many teams prefer creating a new commit (e.g., `git add missed.txt && git commit -m "Add missed.txt"`) instead of amending after pushing, to avoid rewriting shared history.

### Quick best practices
- **Use amend for small local fixes** (typos, forgotten files) before pushing.  
- **Avoid amending public commits** unless you coordinate with your team and understand force-push implications.  
- Use `git reflog` to recover an old commit if you need it after an amend.

