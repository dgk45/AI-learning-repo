# Git Commands --- Complete Step-by-Step Reference

> Practical Git reference for learning Git with PowerShell, Visual
> Studio, and GitHub.

------------------------------------------------------------------------

## 1. Repository Status

``` powershell
git status
```

-\> What is happening right now?

``` powershell
git status -sb
```

-\> Show a short, compact repository status.

``` powershell
git branch
```

-\> What local branches do I have?

``` powershell
git branch -a
```

-\> What local AND remote-tracking branches do I have?

``` powershell
git branch -r
```

-\> What remote-tracking branches do I have?

``` powershell
git branch -vv
```

-\> Which remote branch is each local branch tracking?

------------------------------------------------------------------------

# 2. Create / Clone a Repository

``` powershell
git clone <repository-url>
```

-\> Download a GitHub repository and create a local Git repository.

Example:

``` powershell
git clone https://github.com/dgk45/SampleProgrammers.git
```

``` powershell
git init
```

-\> Create a new Git repository in the current folder.

``` powershell
git init <folder-name>
```

-\> Create a new Git repository inside a new folder.

### Important

`git clone` automatically creates the remote named `origin`.

`git init` does NOT automatically create `origin`.

------------------------------------------------------------------------

# 3. Git Remote

``` powershell
git remote
```

-\> What remote repositories do I have?

``` powershell
git remote -v
```

-\> Where is my GitHub repository?

``` powershell
git remote show origin
```

-\> Show detailed information about the remote named `origin`.

``` powershell
git remote add origin <repository-url>
```

-\> Add a remote repository and call it `origin`.

``` powershell
git remote remove origin
```

-\> Remove the remote named `origin`.

``` powershell
git remote rename origin upstream
```

-\> Rename a remote from `origin` to `upstream`.

``` powershell
git remote get-url origin
```

-\> Show the URL of the `origin` remote.

------------------------------------------------------------------------

# 4. Understanding origin

``` text
origin
    -> Name of the remote GitHub repository.

origin/master
    -> Remote-tracking reference for master on origin.

origin/my-branch
    -> Remote-tracking reference for my-branch on origin.

origin/HEAD -> origin/master
    -> The default branch of the remote named origin is master.
```

### Memory

``` text
origin = WHERE
branch = WHAT
-u     = REMEMBER UPSTREAM
```

------------------------------------------------------------------------

# 5. Git Branch Commands

``` powershell
git branch
```

-\> List local branches.

``` powershell
git branch -r
```

-\> List remote-tracking branches.

``` powershell
git branch -a
```

-\> List local and remote-tracking branches.

``` powershell
git branch <branch-name>
```

-\> Create a new local branch.

Example:

``` powershell
git branch git-learning
```

``` powershell
git switch <branch-name>
```

-\> Switch to another branch.

``` powershell
git checkout <branch-name>
```

-\> Older/general command for switching branches.

``` powershell
git switch -c <branch-name>
```

-\> Create a new branch and switch to it.

``` powershell
git checkout -b <branch-name>
```

-\> Older equivalent: create a branch and switch to it.

``` powershell
git branch -m <old-name> <new-name>
```

-\> Rename a branch.

``` powershell
git branch -m <new-name>
```

-\> Rename the current branch.

``` powershell
git branch -d <branch-name>
```

-\> Delete a local branch if it has been merged.

``` powershell
git branch -D <branch-name>
```

-\> Force-delete a local branch.

``` powershell
git push origin --delete <branch-name>
```

-\> Delete a branch from the remote repository.

------------------------------------------------------------------------

# 6. Stage Changes

``` powershell
git add .
```

-\> Stage all changes under the current directory.

``` powershell
git add <filename>
```

-\> Stage one specific file.

Example:

``` powershell
git add SampleProgrammers.sln
```

``` powershell
git add -A
```

-\> Stage all changes in the entire repository.

``` powershell
git add -u
```

-\> Stage modifications and deletions of already tracked files.

``` powershell
git add -p
```

-\> Interactively choose which parts of a file to stage.

------------------------------------------------------------------------

# 7. Unstage Changes

``` powershell
git restore --staged <filename>
```

-\> Remove a file from staging while keeping its working-tree changes.

Example:

``` powershell
git restore --staged SampleProgrammers.sln
```

``` powershell
git restore --staged .
```

-\> Unstage all staged files.

------------------------------------------------------------------------

# 8. Discard Working-Tree Changes

``` powershell
git restore <filename>
```

-\> Discard unstaged changes in a file and restore it to the last
committed version.

``` powershell
git restore .
```

-\> Discard all unstaged working-tree changes.

> Be careful: discarded changes may be difficult or impossible to
> recover.

------------------------------------------------------------------------

# 9. Commit

``` powershell
git commit -m "message"
```

-\> Create a commit using the staged changes.

Example:

``` powershell
git commit -m "Update solution configuration"
```

``` powershell
git commit
```

-\> Create a commit and open the editor for the commit message.

``` powershell
git commit -am "message"
```

-\> Stage modified/deleted tracked files and commit them.

> This does NOT automatically include new untracked files.

``` powershell
git commit --amend
```

-\> Modify the most recent commit.

``` powershell
git commit --amend -m "New message"
```

-\> Change the most recent commit message.

------------------------------------------------------------------------

# 10. View Commit History

``` powershell
git log
```

-\> What commits exist?

``` powershell
git log --oneline
```

-\> Show commits in compact one-line format.

``` powershell
git log -5 --oneline
```

-\> Show the last 5 commits.

``` powershell
git log -1
```

-\> Show the latest commit.

``` powershell
git log --oneline --decorate --graph --all
```

-\> Show the complete branch/commit history as a graph.

``` powershell
git log --stat
```

-\> Show commits plus file-change statistics.

``` powershell
git log -- <filename>
```

-\> Show the history of a specific file.

``` powershell
git log -p
```

-\> Show commits together with their actual changes.

``` powershell
git log --author="Name"
```

-\> Show commits by a specific author.

``` powershell
git log --since="1 week ago"
```

-\> Show commits from a specified time period.

------------------------------------------------------------------------

# 11. Compare Branch Histories

``` powershell
git log master..my-branch --oneline
```

-\> Show commits that are in `my-branch` but NOT in `master`.

``` powershell
git log my-branch..master --oneline
```

-\> Show commits that are in `master` but NOT in `my-branch`.

``` powershell
git log my-branch..origin/my-branch --oneline
```

-\> Show commits on GitHub but not in my local `my-branch`.

``` powershell
git log origin/my-branch..my-branch --oneline
```

-\> Show commits in my local branch but not on GitHub.

``` powershell
git log --left-right master...my-branch --oneline
```

-\> Show commits unique to either side of the comparison.

------------------------------------------------------------------------

# 12. Diff Commands

``` powershell
git diff
```

-\> What have I changed but NOT staged?

``` powershell
git diff --cached
```

-\> What have I staged that is different from the latest commit?

``` powershell
git diff --staged
```

-\> Same as `git diff --cached`.

``` powershell
git diff master my-branch
```

-\> What changed between master and my-branch?

``` powershell
git diff my-branch origin/my-branch
```

-\> What differs between my local branch and GitHub?

``` powershell
git diff <commit1> <commit2>
```

-\> What changed between two commits?

``` powershell
git diff HEAD
```

-\> What are all working-tree and staged changes compared with HEAD?

``` powershell
git diff HEAD~1 HEAD
```

-\> What changed between the previous commit and the latest commit?

``` powershell
git diff --name-only
```

-\> Show only the names of changed files.

``` powershell
git diff --stat
```

-\> Show a summary of changed files.

------------------------------------------------------------------------

# 13. Show a Commit

``` powershell
git show <commit>
```

-\> Show the commit details and its changes.

Example:

``` powershell
git show 6a3ddf4
```

``` powershell
git show --stat <commit>
```

-\> Show the commit with a summary of changed files.

``` powershell
git show --no-patch <commit>
```

-\> Show commit information without the actual changes.

``` powershell
git show --no-patch --pretty=raw <commit>
```

-\> Show raw commit metadata, including parent commits.

------------------------------------------------------------------------

# 14. Merge

``` powershell
git switch my-branch
git merge git-learning
```

-\> Merge `git-learning` INTO the current `my-branch`.

### Important rule

``` text
git switch RECEIVING-BRANCH
git merge SOURCE-BRANCH
```

Example:

``` text
git-learning
      |
      | merge
      v
  my-branch
```

``` powershell
git merge --no-ff <branch>
```

-\> Create a merge commit even when a fast-forward is possible.

``` powershell
git merge --abort
```

-\> Cancel an in-progress merge and return to the previous state.

------------------------------------------------------------------------

# 15. Fast-Forward Merge

If the current branch has no unique commits:

``` text
A --- B
       \
        C
```

Git can simply move the branch pointer forward:

``` text
A --- B --- C
```

This is called a **fast-forward merge**.

No merge commit is created.

------------------------------------------------------------------------

# 16. Merge Commit

When both branches have different commits:

``` text
       C
      /
A --- B
      \
       D
```

Git can create:

``` text
       C
      / \
A --- B   M
      \ /
       D
```

`M` is the merge commit.

------------------------------------------------------------------------

# 17. Fetch

``` powershell
git fetch origin
```

-\> Download remote changes and update remote-tracking references.

-\> Does NOT merge those changes into the current branch.

``` powershell
git fetch
```

-\> Fetch from the configured remote/upstream.

``` powershell
git fetch --all
```

-\> Fetch from all configured remotes.

``` powershell
git fetch --prune
```

-\> Fetch and remove stale remote-tracking references for deleted remote
branches.

------------------------------------------------------------------------

# 18. Compare After Fetch

``` powershell
git fetch origin
```

Then:

``` powershell
git log my-branch..origin/my-branch --oneline
```

-\> Which commits are on GitHub but not locally?

Then:

``` powershell
git diff my-branch origin/my-branch
```

-\> What actual file changes are on GitHub?

Useful workflow:

``` text
git fetch
     ↓
inspect
     ↓
git log / git diff
     ↓
decide whether to pull
```

------------------------------------------------------------------------

# 19. Pull

``` powershell
git pull
```

-\> Fetch remote changes and integrate them into the current branch.

Conceptually:

``` text
git pull
   ≈
git fetch
+
git merge
```

``` powershell
git pull origin my-branch
```

-\> Pull `my-branch` from `origin`.

``` powershell
git pull --rebase
```

-\> Fetch remote changes and replay local commits on top of them.

``` powershell
git pull --ff-only
```

-\> Pull only if Git can fast-forward; do not create a merge commit.

------------------------------------------------------------------------

# 20. Push

``` powershell
git push
```

-\> Send committed changes to the tracked remote branch.

``` powershell
git push origin my-branch
```

-\> Push local `my-branch` to `origin`.

``` powershell
git push -u origin my-branch
```

-\> Push the branch AND set its upstream to `origin/my-branch`.

``` powershell
git push --set-upstream origin my-branch
```

-\> Same purpose as `-u`.

``` powershell
git push --all
```

-\> Push all local branches.

``` powershell
git push --tags
```

-\> Push all local tags.

``` powershell
git push origin <tag-name>
```

-\> Push one specific tag.

``` powershell
git push --delete origin <branch-name>
```

-\> Delete a remote branch.

------------------------------------------------------------------------

# 21. Upstream / Tracking

``` powershell
git branch -vv
```

-\> Show local branches and their upstream branches.

``` powershell
git branch --set-upstream-to=origin/my-branch my-branch
```

-\> Set an existing local branch to track a remote branch.

``` powershell
git branch --unset-upstream
```

-\> Remove the upstream relationship from the current branch.

### Important

``` text
git push -u origin my-branch
```

means:

``` text
1. Push my-branch to origin.
2. Remember origin/my-branch as its upstream.
```

After that:

``` powershell
git push
git pull
```

usually know where to go automatically.

------------------------------------------------------------------------

# 22. Undo with Restore

``` powershell
git restore <filename>
```

-\> Discard unstaged changes in a file.

``` powershell
git restore --staged <filename>
```

-\> Unstage a file but keep its changes.

``` powershell
git restore --source=<commit> <filename>
```

-\> Restore a file from a specific commit.

------------------------------------------------------------------------

# 23. Reset

``` powershell
git reset --soft HEAD~1
```

-\> Undo the latest commit but keep changes staged.

``` powershell
git reset --mixed HEAD~1
```

-\> Undo the latest commit and unstage the changes, but keep the files
modified.

``` powershell
git reset --hard HEAD~1
```

-\> Undo the latest commit AND discard the changes.

> Be very careful with `--hard`.

### Memory

``` text
--soft
    Commit removed
    Changes staged

--mixed
    Commit removed
    Changes unstaged

--hard
    Commit removed
    Changes discarded
```

------------------------------------------------------------------------

# 24. Revert

``` powershell
git revert <commit>
```

-\> Create a NEW commit that reverses the changes introduced by an
earlier commit.

Use `revert` when you want to undo a commit while preserving shared
history.

### Reset vs Revert

``` text
reset
    -> Move branch history backward.

revert
    -> Create a new commit that undoes an old commit.
```

------------------------------------------------------------------------

# 25. Stash

``` powershell
git stash
```

-\> Temporarily save uncommitted changes.

``` powershell
git stash push -m "My work"
```

-\> Stash changes with a description.

``` powershell
git stash list
```

-\> Show saved stashes.

``` powershell
git stash show
```

-\> Show a summary of the latest stash.

``` powershell
git stash show -p
```

-\> Show the actual stash changes.

``` powershell
git stash pop
```

-\> Apply the latest stash and remove it from the stash list.

``` powershell
git stash apply
```

-\> Apply the latest stash but keep it in the stash list.

``` powershell
git stash apply stash@{0}
```

-\> Apply a specific stash.

``` powershell
git stash drop stash@{0}
```

-\> Delete a specific stash.

``` powershell
git stash clear
```

-\> Delete all stashes.

------------------------------------------------------------------------

# 26. Cherry-Pick

``` powershell
git cherry-pick <commit>
```

-\> Copy the changes from a specific commit and create a new commit on
the current branch.

Example:

``` powershell
git cherry-pick 84bb068
```

### Concept

``` text
Branch A:
A --- B --- C

Branch B:
A --- D

Cherry-pick C into Branch B:

A --- D --- C'
```

`C'` is a new commit containing the changes from `C`.

------------------------------------------------------------------------

# 27. Rebase

``` powershell
git switch my-branch
git rebase master
```

-\> Replay the commits from `my-branch` on top of `master`.

### Concept

Before:

``` text
       C --- D   my-branch
      /
A --- B          master
```

After:

``` text
A --- B --- C' --- D'   my-branch
```

``` powershell
git rebase --abort
```

-\> Cancel an in-progress rebase.

``` powershell
git rebase --continue
```

-\> Continue a rebase after resolving conflicts.

``` powershell
git rebase --skip
```

-\> Skip the current commit during a rebase.

------------------------------------------------------------------------

# 28. Interactive Rebase

``` powershell
git rebase -i HEAD~3
```

-\> Interactively edit the last 3 commits.

Common actions:

``` text
pick
    -> Keep the commit.

reword
    -> Change the commit message.

edit
    -> Stop and modify the commit.

squash
    -> Combine with the previous commit and edit messages.

fixup
    -> Combine with the previous commit and discard this commit's message.

drop
    -> Remove the commit.
```

------------------------------------------------------------------------

# 29. Tags

``` powershell
git tag
```

-\> List tags.

``` powershell
git tag v1.0.0
```

-\> Create a lightweight tag.

``` powershell
git tag -a v1.0.0 -m "Version 1.0.0"
```

-\> Create an annotated tag.

``` powershell
git show v1.0.0
```

-\> Show tag information and the tagged commit.

``` powershell
git push origin v1.0.0
```

-\> Push one tag to GitHub.

``` powershell
git push --tags
```

-\> Push all tags.

``` powershell
git tag -d v1.0.0
```

-\> Delete a local tag.

``` powershell
git push origin --delete v1.0.0
```

-\> Delete a remote tag.

------------------------------------------------------------------------

# 30. Rename / Move Files

``` powershell
git mv old.txt new.txt
```

-\> Rename or move a tracked file and stage the change.

``` powershell
git rm file.txt
```

-\> Delete a tracked file and stage the deletion.

``` powershell
git rm --cached file.txt
```

-\> Stop tracking a file but keep the file on disk.

Useful for `.gitignore` corrections.

------------------------------------------------------------------------

# 31. .gitignore

``` powershell
.gitignore
```

-\> Specifies files/folders Git should normally ignore.

Example:

``` text
bin/
obj/
.vs/
*.user
```

``` powershell
git check-ignore -v <filename>
```

-\> Show which `.gitignore` rule is causing a file to be ignored.

------------------------------------------------------------------------

# 32. Reflog

``` powershell
git reflog
```

-\> Show where HEAD and branch references have recently pointed.

Very useful for recovering from:

-   reset
-   rebase
-   accidental branch movement
-   deleted commits

``` powershell
git reflog --all
```

-\> Show reflog information for all available references.

------------------------------------------------------------------------

# 33. Detached HEAD

``` powershell
git checkout <commit>
```

or:

``` powershell
git switch --detach <commit>
```

-\> Move HEAD directly to a commit instead of a branch.

Check:

``` powershell
git status
```

You may see:

``` text
HEAD detached at <commit>
```

Return to a branch:

``` powershell
git switch my-branch
```

------------------------------------------------------------------------

# 34. Bisect

``` powershell
git bisect start
```

-\> Start a binary search for a problematic commit.

``` powershell
git bisect bad
```

-\> Mark the current commit as bad.

``` powershell
git bisect good <commit>
```

-\> Mark a known-good commit.

Git will move you through commits to identify the commit that introduced
the problem.

``` powershell
git bisect reset
```

-\> End the bisect session.

------------------------------------------------------------------------

# 35. Find a Commit

``` powershell
git log --all --oneline -- <filename>
```

-\> Find commits related to a specific file.

``` powershell
git blame <filename>
```

-\> Show which commit/author last changed each line.

``` powershell
git grep "text"
```

-\> Search tracked files for text.

------------------------------------------------------------------------

# 36. Clean Untracked Files

``` powershell
git clean -n
```

-\> Preview which untracked files would be deleted.

``` powershell
git clean -f
```

-\> Delete untracked files.

``` powershell
git clean -nd
```

-\> Preview deletion of untracked files and directories.

``` powershell
git clean -fd
```

-\> Delete untracked files and directories.

> Be careful with `git clean`. Always use `-n` first.

------------------------------------------------------------------------

# 37. Git Configuration

``` powershell
git config --list
```

-\> Show Git configuration.

``` powershell
git config --global user.name "Your Name"
```

-\> Set your global Git username.

``` powershell
git config --global user.email "you@example.com"
```

-\> Set your global Git email.

``` powershell
git config --global --list
```

-\> Show global Git configuration.

``` powershell
git config --local --list
```

-\> Show configuration for the current repository.

``` powershell
git config --get user.name
```

-\> Show the configured username.

``` powershell
git config --get user.email
```

-\> Show the configured email.

------------------------------------------------------------------------

# 38. Git Version and Help

``` powershell
git --version
```

-\> Show the installed Git version.

``` powershell
git help
```

-\> Show Git help.

``` powershell
git help <command>
```

-\> Open detailed documentation for a command.

Example:

``` powershell
git help rebase
```

``` powershell
git <command> -h
```

-\> Show short help for a command.

Example:

``` powershell
git merge -h
```

------------------------------------------------------------------------

# 39. Git Object / Commit References

``` text
HEAD
    -> The commit currently checked out.

HEAD~
    -> Parent of HEAD.

HEAD~1
    -> Same as HEAD~.

HEAD~2
    -> Two generations before HEAD.

HEAD^
    -> First parent of the current commit.

HEAD^2
    -> Second parent of a merge commit.

<commit-hash>
    -> A specific commit.

master
    -> Local master branch.

my-branch
    -> Local my-branch.

origin/master
    -> Remote-tracking reference for origin's master.

origin/my-branch
    -> Remote-tracking reference for origin's my-branch.
```

------------------------------------------------------------------------

# 40. Important Git Workflow

## Local development

``` text
git status
      ↓
edit files
      ↓
git diff
      ↓
git add .
      ↓
git diff --cached
      ↓
git commit -m "message"
      ↓
git log
```

------------------------------------------------------------------------

## Upload changes to GitHub

``` text
git status
      ↓
git add .
      ↓
git commit -m "message"
      ↓
git push
```

First push of a new branch:

``` text
git push -u origin my-branch
```

------------------------------------------------------------------------

## Get changes from GitHub safely

``` text
git fetch origin
      ↓
git log my-branch..origin/my-branch --oneline
      ↓
git diff my-branch origin/my-branch
      ↓
git pull
```

------------------------------------------------------------------------

## Branch workflow

``` text
git switch master
      ↓
git switch -c my-feature
      ↓
edit files
      ↓
git add .
      ↓
git commit -m "Add feature"
      ↓
git switch master
      ↓
git merge my-feature
```

------------------------------------------------------------------------

# 41. The Most Important Differences

  Command             Main purpose
  ------------------- --------------------------------------------
  `git fetch`         Download remote information
  `git pull`          Fetch + integrate
  `git push`          Upload commits
  `git add`           Stage changes
  `git commit`        Save staged changes
  `git restore`       Restore/discard file changes
  `git reset`         Move branch/HEAD backward
  `git revert`        Create a commit that undoes another commit
  `git merge`         Combine branch histories
  `git rebase`        Replay commits on a new base
  `git cherry-pick`   Copy one commit's changes
  `git stash`         Temporarily store uncommitted work
  `git diff`          Compare changes
  `git log`           View history
  `git reflog`        Recover previous reference positions
  `git tag`           Mark a specific commit
  `git branch`        Manage branches
  `git remote`        Manage remote repositories

------------------------------------------------------------------------

# 42. Simple Git Mental Model

``` text
                    GitHub
                       |
                       |
                    origin
                       |
             +---------+---------+
             |                   |
       origin/master       origin/my-branch
             ↑                   ↑
             | fetch/push/pull   |
             |                   |
          master             my-branch
                                 |
                              HEAD
                                 |
                           Working Tree
                                 |
                              git add
                                 ↓
                               Staging
                                 |
                            git commit
                                 ↓
                              History
```

------------------------------------------------------------------------

# 43. One-Line Memory Guide

``` text
git status
    -> See current state

git branch
    -> See local branches

git switch
    -> Change branch

git add
    -> Stage changes

git diff
    -> See unstaged changes

git diff --cached
    -> See staged changes

git commit
    -> Save staged changes

git log
    -> See history

git show
    -> Inspect a commit

git merge
    -> Combine branches

git rebase
    -> Replay commits on another base

git cherry-pick
    -> Copy a commit

git fetch
    -> Download remote information

git pull
    -> Download + integrate

git push
    -> Upload commits

git remote
    -> Manage GitHub/remotes

git stash
    -> Temporarily hide work

git reset
    -> Move history/reference backward

git revert
    -> Undo with a new commit

git reflog
    -> Find/recover previous HEAD positions

git tag
    -> Mark a commit

git blame
    -> Find who changed each line

git bisect
    -> Find the commit that introduced a bug
```

------------------------------------------------------------------------

# 44. Recommended Learning Order

Follow Git commands in this order:

``` text
01. git clone
02. git status
03. git branch
04. git switch
05. git add
06. git diff
07. git diff --cached
08. git commit
09. git log
10. git show
11. git branch -vv
12. git remote
13. git remote -v
14. git fetch
15. git diff branch1 branch2
16. git pull
17. git push
18. git push -u
19. git merge
20. merge conflicts
21. git restore
22. git reset
23. git revert
24. git stash
25. git cherry-pick
26. git rebase
27. interactive rebase
28. git tag
29. git reflog
30. detached HEAD
31. git bisect
32. git blame
33. git clean
34. advanced Git configuration
```

------------------------------------------------------------------------

# 45. Current Practice Repository

Repository:

``` text
SampleProgrammers
```

Current important branches:

``` text
master
my-branch
git-learning
```

Remote:

``` text
origin
```

Remote-tracking branches:

``` text
origin/master
origin/my-branch
```

Current learning concepts completed:

``` text
✓ status
✓ branch
✓ switch
✓ checkout
✓ add
✓ restore --staged
✓ commit
✓ log
✓ diff
✓ show
✓ merge
✓ fast-forward merge
✓ merge commit
✓ remote
✓ remote -v
✓ branch -r
✓ branch -a
✓ branch -vv
✓ fetch
✓ pull
✓ push
✓ push -u
✓ upstream/tracking
✓ origin
✓ origin/master
✓ origin/my-branch
✓ origin/HEAD
```

------------------------------------------------------------------------

# Quick Cheat Sheet

``` powershell
git status
git branch
git branch -a
git branch -vv
git remote -v

git add .
git diff
git diff --cached
git commit -m "message"

git log --oneline --decorate --graph --all
git show <commit>

git switch <branch>
git switch -c <new-branch>

git merge <branch>

git fetch origin
git log my-branch..origin/my-branch --oneline
git diff my-branch origin/my-branch

git pull
git push
git push -u origin <branch>

git restore <file>
git restore --staged <file>

git reset --soft HEAD~1
git reset --mixed HEAD~1
git reset --hard HEAD~1

git revert <commit>

git stash
git stash list
git stash pop

git cherry-pick <commit>

git rebase master
git rebase -i HEAD~3

git tag
git reflog
git blame <file>
git bisect start
git clean -n
```
