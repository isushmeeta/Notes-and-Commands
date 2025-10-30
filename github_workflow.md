# GitHub Workflow Notes

## 1. General Commands
```bash
git clone <repo-link>                        # Clone repository
git status                                  # Check current status
git branch                                  # List all branches
git checkout main                           # Switch to main branch
git checkout -b <branch-name>              # Create and switch to a new branch
```

## 2. After doing work: Commit & Push
```bash
git add .                                   # Add all changes
git add <file-name>                         # Add specific file
git commit -m "commit message"            # Save changes with a message
git push origin main                        # Push to main branch (primary step)
git push origin <branch-name>              # If on another branch
```

### Optional: Check working state before committing
```bash
git status
git diff
```

### Optional Shortcuts
```bash
git commit -am "message"  # Shortcut for 'add -u' + commit
git push -u                # Set upstream for easier future pushes
```

## 3. Keeping Up-to-date with Fork (Upstream)
### Check if upstream is set
```bash
git remote -v  # Check remotes
```

### If upstream is already set
```bash
git fetch upstream
git merge upstream/main                   # Merge changes from official repo
git push origin main                        # Push updates to your fork
```

### If upstream is not set
```bash
git remote add upstream <official-repo-link>
git fetch upstream                         # Fetch new files from official repo
git merge upstream/main                    # Merge into local main
git push origin main                        # Update your fork
```

## 4. See New Commits Available from Official Repo
```bash
git log HEAD..upstream/main --oneline
git push origin main                        # Push changes back to your fork
```

### Optional (Skip broken hooks)
```bash
git push origin main --no-verify          # Push without verification
```

## 5. Delete Untracked Local Files
```bash
del frontend\www\docs\Home.md
git merge upstream/main
```

### Remove files from Git tracking (delete locally)
```bash
git rm file.txt
git commit -m "Removed file.txt"
git push
```

### Stop tracking a file but keep it locally
```bash
git rm --cached file.txt
```

## 6. Rename or Move Files
### Rename locally (preserve history)
```bash
git mv frontend/www/docs/Home.md frontend/www/docs/Index.md
git commit -m "Renamed Home.md to Index.md"
git push
```

### Rename a file already pushed
```bash
git mv oldname newname                      # Rename locally
git commit -m "Rename oldname to newname"  # Commit change
git push origin main                        # Push to fork
```

## 7. Handle Conflicts When Renaming
```bash
git fetch upstream
git merge upstream/main
git push origin main
```

## 8. Ignore Files Using .gitignore
- Tells Git which files/folders to ignore (e.g., temporary files, local configs, secrets)
- Example entries:
```
# Ignore environment files
.env
# Ignore Python cache
__pycache__/
*.pyc
# Ignore Node modules
node_modules/
```

