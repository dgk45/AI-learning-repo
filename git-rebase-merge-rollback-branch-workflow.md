# Git Rebase, Merge, Rollback & Branch Workflow --- Practical Cheat Sheet

## My Git Setup

-   Main branch: `master`
-   Feature branch: `my-branch`
-   IDE: Visual Studio 2026
-   Remote: `origin`

------------------------------------------------------------------------

# 1. Check Current Status

``` bash
git status
```

Example:

``` text
On branch my-branch

Changes not staged for commit:
  modified: File1.cs
  modified: ImportantFile.cs
```

Use this before doing almost anything.

------------------------------------------------------------------------

# 2. Check Current Branch

``` bash
git branch
```

Example:

``` text
  master
* my-branch
```

`*` means the current branch.

------------------------------------------------------------------------

# 3. Switch Branches

``` bash
git switch master
```

or:

``` bash
git switch my-branch
```

Visual Studio:

**Git → Manage Branches → Checkout**

------------------------------------------------------------------------

# 4. See All Branches

Local branches:

``` bash
git branch
```

Local + remote:

``` bash
git branch -a
```

Example:

``` text
* my-branch
  master
  remotes/origin/master
  remotes/origin/my-branch
```

------------------------------------------------------------------------

# 5. Fetch Remote Changes

``` bash
git fetch origin
```

`fetch` downloads remote information but does not change your working
files.

It updates references such as:

``` text
origin/master
origin/my-branch
```

------------------------------------------------------------------------

# 6. Pull Latest Changes

When you are on `master`:

``` bash
git switch master
git pull origin master
```

Conceptually:

``` text
Remote master
     ↓
  git pull
     ↓
Local master
```

------------------------------------------------------------------------

# 7. View Commit History

``` bash
git log --oneline
```

Example:

``` text
f123456 Update ImportantFile
e234567 Setup changes
d345678 Initial feature
c456789 Master change
```

------------------------------------------------------------------------

# 8. Stage Only One File

If you have:

``` text
File1.cs
File2.cs
ImportantFile.cs
```

and want only `ImportantFile.cs` in the next commit:

``` bash
git add ImportantFile.cs
```

Check:

``` bash
git status
```

Expected:

``` text
Changes to be committed:
    modified: ImportantFile.cs

Changes not staged:
    modified: File1.cs
    modified: File2.cs
```

This is important when you want to move only one logical change.

------------------------------------------------------------------------

# 9. Stage Everything

``` bash
git add .
```

Be careful: this stages all appropriate changes under the current
directory.

------------------------------------------------------------------------

# 10. Commit

``` bash
git commit -m "Update ImportantFile"
```

Example:

``` text
my-branch

A---B---C---X
            ↑
       ImportantFile
```

------------------------------------------------------------------------

# 11. Push Your Branch

``` bash
git push origin my-branch
```

This sends local commits to the remote repository.

------------------------------------------------------------------------

# 12. Merge `master` Into `my-branch`

Suppose:

``` text
master:     A---B---C
                 \
my-branch:        D---E
```

Run:

``` bash
git switch my-branch
git fetch origin
git merge origin/master
```

Result:

``` text
master:     A---B---C
                 \   \
my-branch:        D---E---M
```

`M` is the merge commit.

### Mental model

> Bring the latest `master` changes into my feature branch.

------------------------------------------------------------------------

# 13. Rebase `my-branch` Onto `master`

Instead of merge:

``` bash
git switch my-branch
git fetch origin
git rebase origin/master
```

Before:

``` text
master:     A---B---C
                 \
my-branch:        D---E
```

After:

``` text
master:     A---B---C
                      \
my-branch:             D'---E'
```

Your feature commits are replayed on top of the latest `master`.

### Important

Rebase rewrites the feature branch's commit history.

Normally:

``` text
Rebase my-branch onto master  → normal feature workflow
```

Avoid rewriting a shared/protected `master` unless that is explicitly
intended.

------------------------------------------------------------------------

# 14. Rebase `master` Onto `my-branch`

Technically:

``` bash
git switch master
git rebase my-branch
```

This changes/re-writes `master`.

Example:

``` text
Before:

master:     A---B---C
                 \
my-branch:        D---E
```

After:

``` text
A---B---D---E---C'
```

For a protected/shared `master`, this is normally not the workflow you
want.

### Remember

The branch you switch to is the branch being changed.

``` bash
git switch my-branch
git rebase master
```

Changes `my-branch`.

``` bash
git switch master
git rebase my-branch
```

Changes `master`.

------------------------------------------------------------------------

# 15. Stash Uncommitted Changes

Useful when local debugging/setup changes must be preserved while you
update or rebase the branch.

``` bash
git stash push -u -m "Local debugging setup"
```

`-u` also includes untracked files.

After stash:

``` text
Working directory = clean
```

Your changes are stored in Git's stash.

------------------------------------------------------------------------

# 16. See Stashes

``` bash
git stash list
```

Example:

``` text
stash@{0}: On my-branch: Local debugging setup
stash@{1}: On master: temporary work
```

------------------------------------------------------------------------

# 17. Restore Stash

Restore and remove the latest stash if successful:

``` bash
git stash pop
```

Restore but keep the stash:

``` bash
git stash apply
```

Difference:

``` text
stash pop
    ↓
restore + remove stash

stash apply
    ↓
restore + keep stash
```

------------------------------------------------------------------------

# 18. Update `my-branch` While Keeping Local Debugging Changes

Scenario:

``` text
master:     A---B---C---D
                 \
my-branch:        E---F

Local uncommitted setup changes:
    File1.cs
    File2.cs
    ImportantFile.cs
```

Recommended workflow:

``` bash
git switch my-branch

git stash push -u -m "Local debugging setup"

git fetch origin

git rebase origin/master

git stash pop
```

Result:

``` text
master:     A---B---C---D
                      \
my-branch:             E'---F'

Local debugging changes:
    still uncommitted
```

### Can `stash pop` conflict?

Yes.

If `master` changed the same lines that your local debugging changes
modify, Git may report a conflict.

If `master` did not change the relevant lines, Git will normally restore
the stash automatically.

------------------------------------------------------------------------

# 19. Cherry-Pick One Commit

Cherry-pick means:

> Take one specific commit from another branch and apply its changes to
> the current branch.

Example:

``` text
my-branch:

A---B---C---X
            ↑
       ImportantFile
```

Switch to `master`:

``` bash
git switch master
```

Then:

``` bash
git cherry-pick X
```

Result:

``` text
master:

A---B---C---X'
```

`X'` is a new commit containing the changes from `X`.

### Important

Cherry-pick works at the **commit level**, not directly at the file
level.

If a commit contains:

``` text
Commit X
 ├── ImportantFile.cs   ✅
 ├── Setup1.cs           ❌
 └── Setup2.cs           ❌
```

then cherry-picking X brings all three changes.

------------------------------------------------------------------------

# 20. Your One-File-to-Master Scenario

You have no commits yet.

Your `my-branch` has:

``` text
File1.cs          setup change
File2.cs          setup change
ImportantFile.cs  change that should go to master
File3.cs          setup change
```

First, commit only the required file:

``` bash
git switch my-branch

git add ImportantFile.cs

git commit -m "Update ImportantFile"
```

The other setup files remain uncommitted.

Then update `master`:

``` bash
git switch master
git pull origin master
```

Find the commit ID:

``` bash
git log --oneline
```

Then cherry-pick it:

``` bash
git cherry-pick <commit-id>
```

Now `master` receives only that committed change.

### Visual Studio equivalent

1.  Open **Git Changes**.
2.  Stage only `ImportantFile.cs`.
3.  Commit Staged.
4.  Switch to `master`.
5.  Pull latest `master`.
6.  Open **Git → Manage Branches**.
7.  Find the commit.
8.  Right-click → **Cherry-Pick**.

------------------------------------------------------------------------

# 21. Cherry-Pick Conflict

If Git reports:

``` text
CONFLICT (content): Merge conflict in ImportantFile.cs
```

Check:

``` bash
git status
```

Fix the conflicted file.

Then:

``` bash
git add ImportantFile.cs
git cherry-pick --continue
```

If you want to cancel:

``` bash
git cherry-pick --abort
```

### Why can cherry-pick conflict?

A conflict can happen when the selected commit changes an area that has
also changed differently on `master`.

------------------------------------------------------------------------

# 22. Merge Conflict

During:

``` bash
git merge origin/master
```

you may get:

``` text
CONFLICT (content)
```

Check:

``` bash
git status
```

Fix the conflicted files.

Then:

``` bash
git add .
git commit
```

Cancel the merge:

``` bash
git merge --abort
```

------------------------------------------------------------------------

# 23. Rebase Conflict

During:

``` bash
git rebase origin/master
```

you may get a conflict.

Fix the file, then:

``` bash
git add ImportantFile.cs
git rebase --continue
```

Cancel the complete rebase:

``` bash
git rebase --abort
```

------------------------------------------------------------------------

# 24. Conflict Recovery Cheat Sheet

``` text
Merge conflict
    ↓
git merge --abort

Rebase conflict
    ↓
git rebase --abort

Cherry-pick conflict
    ↓
git cherry-pick --abort
```

These commands return you to the state before the respective operation.

------------------------------------------------------------------------

# 25. Undo Last Commit --- Keep Changes Staged

Suppose:

``` text
A---B---C
        ↑
       HEAD
```

Remove commit C but keep its changes staged:

``` bash
git reset --soft HEAD~1
```

Result:

``` text
A---B

Changes from C remain staged.
```

------------------------------------------------------------------------

# 26. Undo Last Commit --- Keep Changes Unstaged

``` bash
git reset HEAD~1
```

The commit is removed locally, but the file changes remain in your
working directory.

------------------------------------------------------------------------

# 27. Undo Last Commit AND Discard Changes

``` bash
git reset --hard HEAD~1
```

WARNING: `--hard` can discard uncommitted work.

Use carefully.

------------------------------------------------------------------------

# 28. Revert a Pushed/Shared Commit

If a commit is already pushed and other people may have pulled it:

``` bash
git revert <commit-id>
```

Example:

``` bash
git revert f123456
```

This creates a new commit that reverses the old commit.

For shared branches, this is generally safer than rewriting history with
reset.

------------------------------------------------------------------------

# 29. View Working Changes

``` bash
git diff
```

Shows unstaged changes.

For staged changes:

``` bash
git diff --staged
```

------------------------------------------------------------------------

# 30. Compare `master` and `my-branch`

Show commits on `my-branch` that are not on `master`:

``` bash
git log master..my-branch --oneline
```

Show commits on `master` that are not on `my-branch`:

``` bash
git log my-branch..master --oneline
```

Compare file changes:

``` bash
git diff master..my-branch
```

------------------------------------------------------------------------

# 31. Reflog --- Recover From Mistakes

``` bash
git reflog
```

Example:

``` text
abc1234 HEAD@{0}: rebase finished
def5678 HEAD@{1}: checkout master
ghi9012 HEAD@{2}: commit: ImportantFile
```

`reflog` is very useful when you accidentally reset, rebase, or move a
branch and need to find an earlier HEAD position.

------------------------------------------------------------------------

# 32. Create a New Branch

``` bash
git switch -c my-branch
```

This creates the branch and switches to it.

------------------------------------------------------------------------

# 33. Delete a Local Branch

Normal deletion:

``` bash
git branch -d my-branch
```

Force deletion:

``` bash
git branch -D my-branch
```

`-D` can delete a branch containing unmerged commits, so use it
carefully.

------------------------------------------------------------------------

# 34. Practical Workflow --- Feature Development

Typical workflow:

``` bash
git switch my-branch

git fetch origin

git rebase origin/master
```

Work on your feature.

Then:

``` bash
git status
git add .
git commit -m "Implement feature"
git push origin my-branch
```

Create a Pull Request to `master` if your repository uses PR-based
development.

------------------------------------------------------------------------

# 35. Practical Workflow --- Local Debugging Setup

If your local setup files are required for debugging but should not be
included in the current commit:

``` bash
git status
```

Stash them:

``` bash
git stash push -u -m "Local debugging setup"
```

Update your branch:

``` bash
git fetch origin
git rebase origin/master
```

Restore your local setup:

``` bash
git stash pop
```

Continue debugging.

------------------------------------------------------------------------

# 36. Practical Workflow --- One File to `master`

Scenario:

``` text
my-branch
 ├── File1.cs          local setup
 ├── File2.cs          local setup
 ├── File3.cs          local setup
 └── ImportantFile.cs  should go to master
```

Do:

``` bash
git add ImportantFile.cs
git commit -m "Update ImportantFile"
```

Then:

``` bash
git switch master
git pull origin master
git cherry-pick <commit-id>
```

Then:

``` bash
git push origin master
```

If `master` is protected, push a separate branch and create a Pull
Request instead.

------------------------------------------------------------------------

# 37. Merge vs Rebase vs Cherry-Pick

  -----------------------------------------------------------------------
  Operation               Meaning                 Typical use
  ----------------------- ----------------------- -----------------------
  `merge`                 Combine branch          Bring `master` into
                          histories               feature or feature into
                                                  `master`

  `rebase`                Replay commits on a new Keep feature branch
                          base                    current/linear

  `cherry-pick`           Copy one specific       Move a specific change
                          commit                  without merging the
                                                  whole branch

  `stash`                 Temporarily store       Safely switch/rebase
                          uncommitted work        while keeping local
                                                  changes

  `revert`                Create a new commit     Safely undo
                          that undoes another     shared/pushed changes
                          commit                  

  `reset`                 Move branch/HEAD        Local history
                          backward                cleanup/rollback

  `reflog`                Find previous HEAD      Recovery after mistakes
                          positions               
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# 38. The Most Important Mental Model

## `merge`

> Bring histories together.

``` bash
git merge master
```

## `rebase`

> Put my commits on top of another base.

``` bash
git rebase master
```

## `cherry-pick`

> Bring this particular commit over.

``` bash
git cherry-pick <commit-id>
```

## `stash`

> Temporarily put my uncommitted changes aside.

``` bash
git stash
```

## `revert`

> Create a new commit that undoes an old commit.

``` bash
git revert <commit-id>
```

## `reset`

> Move my branch/HEAD backward.

``` bash
git reset ...
```

------------------------------------------------------------------------

# 39. Eight Commands to Learn First

``` bash
git status
git switch <branch>
git fetch origin
git pull
git add <file>
git commit -m "message"
git stash
git rebase
```

Then learn:

``` bash
git merge
git cherry-pick
git revert
git reset
git reflog
```

------------------------------------------------------------------------

# 40. Three Emergency Commands to Memorize

``` bash
# Cancel rebase
git rebase --abort

# Cancel merge
git merge --abort

# Cancel cherry-pick
git cherry-pick --abort
```

These are useful when experimenting with Git and you need to safely back
out of an operation.

------------------------------------------------------------------------

# Quick Reference

``` text
Check status
    git status

Switch branch
    git switch my-branch

Fetch remote
    git fetch origin

Pull
    git pull origin master

Stage one file
    git add ImportantFile.cs

Commit
    git commit -m "message"

Push
    git push origin my-branch

Merge master into my-branch
    git switch my-branch
    git merge origin/master

Rebase my-branch onto master
    git switch my-branch
    git rebase origin/master

Stash local changes
    git stash push -u -m "Local debugging setup"

Restore stash
    git stash pop

Cherry-pick one commit
    git switch master
    git cherry-pick <commit-id>

Abort merge
    git merge --abort

Abort rebase
    git rebase --abort

Abort cherry-pick
    git cherry-pick --abort

Undo last local commit, keep changes
    git reset --soft HEAD~1

Safely undo shared commit
    git revert <commit-id>

Find lost commits
    git reflog
```
