# Git Learning Quick Reference: Reset, Revert, Force Push, and Pull Requests

## 1. Quick reference

| Command / action | What it does | Main impact | When to use |
|---|---|---|---|
| `git reset --soft HEAD~1` | Moves the current local branch back one commit | Removes the latest commit from the local branch; keeps its changes **staged** | Redo your latest local commit, such as changing its message or adding files |
| `git revert HEAD` | Creates a new commit that reverses the latest commit | Keeps the original commit in history and adds an undo commit | Safely undo a commit that has already been pushed or shared |
| `git push --force-with-lease` | Updates the remote branch to match your rewritten local history, if the remote is still at the expected state | Can replace remote branch history | After intentionally rewriting your own branch history; use cautiously |
| `git push -u origin <branch>` | Pushes a branch and sets its upstream tracking branch | Publishes the branch and configures the default remote branch for later `git push` / `git pull` | First push of a new branch |
| `git revert -m 1 <merge-commit>` | Reverts a merge commit while keeping its first parent as the mainline | Adds a commit that reverses the merged changes | Reverting a PR that was merged with a merge commit |
| `gh pr create --base master --head <branch>` | Creates a pull request using GitHub CLI | Opens a PR from your feature branch into `master` | If GitHub CLI is installed and authenticated |

## 2. Understand the three main commands

### `git reset --soft HEAD~1`

Before:

```text
A -- B -- C  (HEAD)
```

Run:

```powershell
git reset --soft HEAD~1
```

After:

```text
A -- B  (HEAD)

Changes introduced by C remain staged.
```

Important points:
- `HEAD~1` means the parent of the current commit.
- This changes your **local branch history**.
- It does not automatically update GitHub.
- If the commit was already pushed, updating the remote may require `git push --force-with-lease`.
- On a shared branch, rewriting history can disrupt teammates. Prefer adding a new commit unless you have a good reason to rewrite history.

### `git revert HEAD`

Before:

```text
A -- B -- C  (HEAD)
```

Run:

```powershell
git revert HEAD
```

After:

```text
A -- B -- C -- D  (HEAD)
```

`D` is a new commit that reverses the changes introduced by `C`.

Important points:
- The original commit remains in history.
- A new undo commit is created.
- This is usually safer for commits already pushed or shared.
- A normal `git push` is usually sufficient after the revert.
- Resolve any conflicts if Git reports them; do not assume a revert succeeded until you check the output and `git status`.

### `git push --force-with-lease`

Important points:
- This command does **not** undo a commit by itself.
- It updates the remote branch after you have rewritten local history.
- The “lease” checks that the remote branch has not changed unexpectedly since your last remote-tracking update.
- It is safer than plain `git push --force`, but it can still remove commits from the remote branch's visible history.
- Avoid it on shared branches and protected branches unless your team explicitly permits it.

## 3. Raising a pull request (PR)

Example: create a feature branch named `feature-login` and raise a PR into `master`.

### Step 1 — Update `master` and create a branch

```powershell
git switch master
git pull origin master
git switch -c feature-login
```

### Step 2 — Make changes and commit

```powershell
git status
git add .
git commit -m "Add login feature"
```

Review your changes before committing. `git add .` stages all eligible changes under the current directory, so check `git status` first.

### Step 3 — Push the branch

```powershell
git push -u origin feature-login
```

The `-u` option sets the upstream tracking branch. After this initial push, you can normally use `git push` and `git pull` while on that branch.

### Step 4 — Create the PR

Using GitHub CLI (if installed and authenticated):

```powershell
gh pr create --base master --head feature-login --title "Add login feature" --body "Implements the login feature."
```

Or use GitHub in a browser:
1. Open the repository on GitHub.
2. Select **Pull requests** → **New pull request**.
3. Set **base** to `master` and **compare** to `feature-login`.
4. Review the changes and create the PR.

### Step 5 — Update an open PR

Make additional changes on the same feature branch, commit, and push:

```powershell
git add .
git commit -m "Fix login validation"
git push
```

The existing PR normally updates automatically. You do not need to create a new PR for every commit on the same branch.

## 4. Reverting after a PR has been merged

### Recommended team workflow

If the merged change must be undone, normally create a new branch, revert the relevant commit(s), push that branch, and raise a new PR. Avoid resetting or force-pushing the shared target branch such as `master`.

```powershell
git switch master
git pull origin master
git switch -c revert-pr-123
```

Then identify what was merged:

```powershell
git log --oneline --graph
```

**If reverting a normal commit:**

```powershell
git revert <commit-hash>
```

**If reverting a merge commit:**

```powershell
git revert -m 1 <merge-commit-hash>
```

`-m 1` tells Git to treat the first parent as the mainline (usually the target branch). Confirm the commit is a merge commit before using this command.

Then push the revert branch:

```powershell
git push -u origin revert-pr-123
```

Create a PR from `revert-pr-123` into `master`, and follow your team's review process.

Notes:
- GitHub may offer a **Revert** button on an eligible merged PR.
- The right command depends on the merge method: merge commit, squash merge, or rebase merge.
- Do not push directly to `master` if your repository requires PRs or protects the branch.
- Reverting a PR does not delete the original PR or its commits from history.

## 5. What changes after raising a PR?

| Situation | Typical approach | Caution |
|---|---|---|
| PR is open; you need to add a fix | Commit and `git push` to the same branch | The existing PR updates automatically |
| PR is open; you want to redo your latest commit | `git reset --soft HEAD~1`, recommit, then possibly `git push --force-with-lease` | Rewrites branch history; coordinate if others use the branch |
| PR is open; you need to undo a pushed commit | `git revert <commit-hash>` and push | Usually adds an undo commit to the PR |
| PR is already merged; you need to undo it | Revert on a new branch and raise another PR | Merge-commit revert may require `-m 1` |
| You rewrote local history and must update your remote branch | `git push --force-with-lease` | Do not overwrite unexpected teammate changes |

## 6. Safety checklist before running commands

1. Run `git status` to check whether you have staged or unstaged changes.
2. Run `git log --oneline --graph --decorate -5` to inspect recent history.
3. Confirm the current branch with `git branch --show-current`.
4. Before a reset or force push, make sure you understand which commits will move or disappear from the branch history.
5. Do not use force push as a substitute for `git revert`.
6. On shared or protected branches, follow your team's PR and review rules.
7. After an operation, verify the result with:

```powershell
git status
git log --oneline --graph --decorate -5
```

## 7. Easy memory rule

- **Reset** = move the branch pointer backward; `--soft` keeps changes staged.
- **Revert** = add a new commit that undoes changes.
- **Force-with-lease** = update the remote after intentionally rewriting history.
- **Push** = send commits to the remote.
- **PR** = ask for review and merge changes into the target branch.
- **Revert a merged PR** = undo it with a revert and normally send that undo through a new PR.

## 8. Cherry-pick: apply selected commits

`git cherry-pick` applies the changes introduced by selected commit(s) to the **current branch**. It creates new commit(s), so the new commit hashes usually differ from the source commit hashes.

### One specific commit

```powershell
git cherry-pick <commit-hash>
```

Example from the practice repository:

```powershell
git cherry-pick 0234e94
```

This applied the changes from `Practice git revert` to the current practice branch and created a new commit (`032f1b0` in that exercise).

### Multiple specific commits

List the hashes in the order you want Git to apply them:

```powershell
git cherry-pick <commit1> <commit2> <commit3>
```

Example:

```powershell
git cherry-pick 0234e94 5065152
```

There is **no fixed maximum number of commit hashes** for this command. Practical limits include command-line length and the complexity of the changes. List only the commits you actually want.

### A range of commits

Use a range when you want the commits reachable from the ending reference but not from the starting reference:

```powershell
git cherry-pick <start>..<end>
```

Example from the practice repository:

```powershell
git cherry-pick 30356ce..revert-practice
```

Preview the selected commits first:

```powershell
git log --oneline 30356ce..revert-practice
```

In the exercise, this selected:

```text
5065152 Revert "Practice git revert"
0234e94 Practice git revert
```

The range excludes the starting commit (`30356ce`). It is not simply a count of commits between two lines in the log; Git selects commits reachable from the end that are not reachable from the start.

### What the practice showed

- `0234e94` added four lines to `SampleProgrammers/Program.cs`.
- `5065152` reverted those same four lines.
- Cherry-picking both commits onto a clean branch created two new commits, while the final file content matched `master`.
- The original commits remained on `revert-practice`; the cherry-picked commits had new hashes on the target branch.

### Cherry-pick versus merge

| Cherry-pick | Merge |
|---|---|
| Applies selected commit(s) to the current branch | Integrates changes from another branch |
| Useful when you need only particular changes | Useful when you want to bring a branch's work together |
| Creates new commit(s) with new hashes | May create a merge commit or fast-forward |
| Does not automatically bring the source branch's whole history | Integrates branch histories according to the merge strategy |

### Cherry-pick quick reference

| Goal | Command |
|---|---|
| Apply one commit | `git cherry-pick A` |
| Apply two selected commits | `git cherry-pick A B` |
| Apply three or more selected commits | `git cherry-pick A B C` |
| Preview a range | `git log --oneline A..B` |
| Apply a range | `git cherry-pick A..B` |
| Check the resulting history | `git log --oneline --graph --decorate` |
| Check whether the working tree is clean | `git status` |

Important:
- Run cherry-pick while checked out on the **destination branch**.
- Check `git status` first and start with a clean working tree.
- If Git reports a conflict, resolve the files and then use `git add <file>` followed by `git cherry-pick --continue`; use `git cherry-pick --abort` to cancel the in-progress operation.
- Do not repeatedly cherry-pick commits that have already been applied unless you intentionally want to apply their changes again.
- If the selected commits add and then remove the same changes, the final files can match the base branch even though the new commits remain in history.

## 9. Search GitHub Pull Request (PR) titles from PowerShell

Git itself does not provide a built-in command that directly searches GitHub PR titles. `git log --grep` searches **commit messages**, which can be different from PR titles. To search PR titles from the terminal, use **GitHub CLI (`gh`)**.

### Search PR titles for a keyword

```powershell
gh search prs "login" --repo dgk45/SampleProgrammers --match title
```

This searches PR titles containing `login` in the `dgk45/SampleProgrammers` repository.

### Search merged PR titles

```powershell
gh search prs "login" --repo dgk45/SampleProgrammers --match title --merged
```

### Search using `gh pr list`

```powershell
gh pr list --state all --search "in:title login"
```

This searches PRs in the current repository across all states, with `login` in the title.

### Compare the commands

| Goal | Command |
|---|---|
| Search Git commit messages | `git log --all --oneline --grep="login" -i` |
| Search GitHub PR titles | `gh search prs "login" --repo dgk45/SampleProgrammers --match title` |
| Search merged PR titles | `gh search prs "login" --repo dgk45/SampleProgrammers --match title --merged` |
| Search PR titles from the current repository | `gh pr list --state all --search "in:title login"` |
| Search PR titles in GitHub's web interface | `is:pr in:title login` |

Notes:
- Install GitHub CLI (`gh`) and authenticate with `gh auth login` if needed.
- `gh search prs` searches PRs on GitHub; it requires network access and appropriate repository visibility/permissions.
- `git log --grep` cannot reliably find a PR by title because PR titles and commit messages are separate pieces of metadata.
