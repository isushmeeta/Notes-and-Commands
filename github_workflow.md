# GitHub Workflow Notes

## 1. General Commands
```bash
git clone <repo-link>                        # Clone repository
cd projectfoldername
git status                                  # Check current status
git branch                                  # List all branches
```
## Always create a new Branch Before working

```bash
git checkout main                          # Switch to main branch
git pull origin main                       # Get latest main branch
git checkout -b <branch-name>              # Create and switch to a new branch
```

## 2. After doing work: Commit & Push
```bash
git add .                                   # Add all changes
git add <file-name>                         # Add specific file
git commit -m "commit message"              # Save changes with a message
git push origin main                        # Push to main branch (primary step)
git push origin <branch-name>               # If on another branch
```
### Optional: Check working state before committing
```bash
git status
git diff
```
### To Delete a branch 
```bash
git checkout main                            # Switch to main branch
git branch -d branchname                     # Delete Branch Locally
git push origin --delete branchname          # Delete branch from GitHub (remote)
```

### Optional Shortcuts
```bash
git commit -am "message"  # Shortcut for 'add -u' + commit
git push -u                # Set upstream for easier future pushes:#when you work in the same directory for a long lime u can write -u to set it upstream then u dont have to write origin main multiples time, u just write git push
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
-After that, the local and GitHub fork will both be up to date with the latest main branch

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
-After that, the file disappears from both local repo and GitHub.

## Optional:
### Stop tracking a file but keep it locally
```bash
git rm --cached file.txt
```

## 6. Rename or Move Files
### Rename locally (preserve history):
```bash
git mv frontend/www/docs/Home.md frontend/www/docs/Index.md
git commit -m "Renamed Home.md to Index.md"
git push
```

### Rename a file already pushed to github:
```bash
git mv oldname newname                      # Rename locally
git commit -m "Rename oldname to newname"  # Commit change
git push origin main                        # Push to fork
```

## 7. If someone else’s changes conflict with that file name, this handles conflicts When Renaming
```bash
git fetch upstream
git merge upstream/main
git push origin main
```

## 8. Ignore Files Using .gitignore
- Tells Git which files/folders to ignore (e.g., temporary files, local configs, secrets)
- Example entries:
```bash
.env                   # Ignore environment files
__pycache__/           # Ignore Python cache
*.pyc 
node_modules/          # Ignore Node modules
```

