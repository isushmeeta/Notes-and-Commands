# 🧭 GitHub Workflow Notes

## 🔹 General Commands
```bash
git clone <repo-link>                     # Clone repository
git status                                # Check current status
git branch                                # List all branches
git checkout main                         # Switch to main branch
git checkout -b <branch-name>             # Create and switch to a new branch
```

## 🔹 After Doing Work (Commit & Push)
```bash
git add .                                 # Add all changes
git add <file-name>                       # Add specific file
git commit -m "commit message"            # Save changes with a message
git push origin main                      # Push to main branch (primary step)
```

## 🔹 Optional Shortcuts
```bash
git commit -am "message"                  # Shortcut for 'add -u' + 'commit'
git push -u origin main                   # Set upstream (so next time just 'git push')
```
If you’re on another branch:
```bash
git push origin <branch-name>
```

## 🔹 Sync Fork with Original Repo (Upstream)
### Step 1: Check if upstream is set
```bash
git remote -v                             # Check remotes
```
### Step 2A: If upstream already set
```bash
git fetch upstream
git merge upstream/main                   # Merge changes from official repo
git push origin main                      # Push updates to your fork
```
### Step 2B: If upstream not set
```bash
git remote add upstream <official-repo-link>
git fetch upstream                        # Fetch new files from official repo
git merge upstream/main                   # Merge into local main
git push origin main                      # Update your fork
```
### Step 3: Check if new commits exist
```bash
git log HEAD..upstream/main --oneline
```
### Optional (skip broken hooks)
```bash
git push origin main --no-verify
```

## 🔹 Remove or Replace Files
### Remove untracked local file
```bash
del frontend\www\docs\Home.md
git merge upstream/main
```
### Remove file from Git tracking (and delete it)
```bash
git rm file.txt
git commit -m "Removed file.txt"
git push
```
### Stop tracking (keep file locally)
```bash
git rm --cached file.txt
```

## 🔹 Rename or Move Files
### Rename or move file (keeps history)
```bash
git mv frontend/www/docs/Home.md frontend/www/docs/Index.md
git commit -m "Renamed Home.md to Index.md"
git push
```
### Rename file already pushed to repo
```bash
git mv oldname newname
git commit -m "Rename oldname to newname"
git push origin main
```
If there are conflicts:
```bash
git fetch upstream
git merge upstream/main
git push origin main
```

✅ **Tip:**
You can always check your working state before committing:
```bash
git status
git diff
```
