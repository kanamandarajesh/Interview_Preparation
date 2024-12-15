Here are **20 scenario-based Git interview questions and answers** to help you prepare for your Git-focused interview:

---

### 1. **Scenario**: *You’ve made changes in your working directory, but you haven’t committed them yet. How can you check which files have been modified?*

**Answer**:
Use the `git status` command:
```bash
git status
```
This will show you a list of modified files and files that are staged for the next commit.

---

### 2. **Scenario**: *You committed a file, but later realized it contains sensitive data like passwords. How do you remove this file from the commit history?*

**Answer**:
To remove sensitive data from the commit history, you can use `git filter-branch` or `BFG Repo-Cleaner`. Here’s an example using `git filter-branch`:

```bash
git filter-branch --force --index-filter \
"git rm --cached --ignore-unmatch path/to/file" \
--prune-empty --tag-name-filter cat -- --all
```

Alternatively, you can use **BFG Repo-Cleaner** for a simpler and faster approach:
```bash
bfg --delete-files 'filename' <your-repo-dir>
```

Then, you must force-push the changes to the remote:
```bash
git push --force --all
```

---

### 3. **Scenario**: *You’ve made several commits, but you want to squash them into a single commit before pushing them to the remote. How do you do that?*

**Answer**:
Use **interactive rebase** to squash the commits:
```bash
git rebase -i HEAD~n  # n is the number of commits to go back
```

In the editor that opens, change the word "pick" to "squash" (or "s") for the commits you want to combine. Then, save and close the editor. Finally, push the changes:
```bash
git push --force
```

---

### 4. **Scenario**: *You accidentally committed a large file and pushed it to the remote repository. How do you remove this file from the Git history?*

**Answer**:
You can use `git filter-branch` or **BFG Repo-Cleaner** (which is faster). Here’s an example using `git filter-branch`:

```bash
git filter-branch --force --index-filter \
"git rm --cached --ignore-unmatch path/to/large-file" \
--prune-empty --tag-name-filter cat -- --all
```

Then force-push the changes:
```bash
git push --force
```

Afterward, run `git gc` to clean up any leftover data:
```bash
git gc --prune=now
```

---

### 5. **Scenario**: *How do you undo the most recent commit while keeping the changes in your working directory?*

**Answer**:
Use the `git reset` command:
```bash
git reset --soft HEAD~1
```
This will undo the most recent commit but keep your changes staged (ready to commit again).

To keep the changes but unstage them:
```bash
git reset HEAD~1
```

---

### 6. **Scenario**: *You accidentally merged a branch, and now you want to undo the merge. How do you do this?*

**Answer**:
To undo a merge commit, you can use `git reset`:
```bash
git reset --hard HEAD~1
```

This will reset the repository to the state before the merge. However, be cautious as this will discard any uncommitted changes. Alternatively, if you have already pushed the merge commit, you can use `git revert`:
```bash
git revert -m 1 <merge_commit_sha>
```
This creates a new commit that undoes the changes from the merge commit.

---

### 7. **Scenario**: *You want to see the history of changes made to a specific file in Git. How can you do that?*

**Answer**:
Use `git log` with the `--follow` option:
```bash
git log --follow path/to/file
```
This shows the commit history for the specified file, including changes made before it was renamed.

---

### 8. **Scenario**: *You cloned a repository, but now you want to check for any updates in the remote repository. How do you do that?*

**Answer**:
To check for updates from the remote, use:
```bash
git fetch
```

This will download changes from the remote repository without modifying your working directory. To see the differences, you can use:
```bash
git diff origin/main
```

To merge the updates, run:
```bash
git pull
```

---

### 9. **Scenario**: *You want to create a new branch from the current branch. How do you do this?*

**Answer**:
To create a new branch and switch to it, use:
```bash
git checkout -b new-branch-name
```
Alternatively, if you just want to create the branch without switching to it:
```bash
git branch new-branch-name
```

---

### 10. **Scenario**: *You need to create a pull request (PR) from a feature branch. How would you do this?*

**Answer**:
1. First, commit and push your changes:
   ```bash
   git push origin feature-branch
   ```

2. Then, go to your Git hosting service (GitHub, GitLab, Bitbucket) and open a pull request to merge the `feature-branch` into the `main` (or relevant) branch.

---

### 11. **Scenario**: *You want to see what changes were made between two commits. How do you do this?*

**Answer**:
Use `git diff` with the commit hashes:
```bash
git diff <commit1> <commit2>
```
This will show the differences between the two commits.

---

### 12. **Scenario**: *You made changes to a file but decided you don’t want those changes anymore. How do you discard them?*

**Answer**:
To discard changes in the working directory:
```bash
git checkout -- path/to/file
```

If the file was staged, use:
```bash
git reset HEAD path/to/file
```

Then, discard the changes:
```bash
git checkout -- path/to/file
```

---

### 13. **Scenario**: *You want to check the difference between your local branch and the remote branch. How do you do that?*

**Answer**:
Use `git fetch` to update the remote references and then:
```bash
git diff origin/main..main
```
This will show the differences between your local `main` branch and the remote `origin/main` branch.

---

### 14. **Scenario**: *How can you list all branches (local and remote) in a Git repository?*

**Answer**:
To list local branches:
```bash
git branch
```

To list remote branches:
```bash
git branch -r
```

To list both local and remote branches:
```bash
git branch -a
```

---

### 15. **Scenario**: *You’re working on a branch and want to fetch changes from the remote repository without merging them. How do you do this?*

**Answer**:
Use `git fetch` to get the latest changes without merging:
```bash
git fetch origin
```

Then, you can inspect the changes or decide how to integrate them.

---

### 16. **Scenario**: *You want to find the commit hash of the first commit in the repository. How do you do that?*

**Answer**:
Use the following command:
```bash
git log --reverse
```
This will display the commit history starting from the first commit.

Alternatively:
```bash
git rev-list --max-parents=0 HEAD
```

---

### 17. **Scenario**: *You want to remove a file from the staging area but keep it in your working directory. How do you do that?*

**Answer**:
Use `git reset`:
```bash
git reset path/to/file
```
This removes the file from the staging area but leaves the changes in your working directory.

---

### 18. **Scenario**: *How do you merge changes from one branch into another?*

**Answer**:
1. Checkout the branch you want to merge changes into:
   ```bash
   git checkout target-branch
   ```

2. Merge the source branch into the target branch:
   ```bash
   git merge source-branch
   ```

---

### 19. **Scenario**: *You need to remove a file from both the working directory and the Git history. How do you do that?*

**Answer**:
To remove a file from both the working directory and the Git history, use:
```bash
git rm --cached path/to/file
git commit -m "Remove sensitive file"
```

Then, use `git filter-branch` or BFG Repo-Cleaner to remove the file from the commit history.

---

### 20. **Scenario**: *How do you clone a specific branch from a Git repository?*

**Answer**:
Use the `-b` option when cloning:
```bash
git clone -b branch-name <repository-url>
```

This clones the specific branch `branch-name` from the repository.

---

These 20 scenario-based Git interview questions will help you understand common real-world scenarios that require knowledge of Git commands and workflows.
