# Git + GitHub CLI (`gh`) Command Reference — Windows CMD

**Completeness note:** Git and `gh` change by version, and extensions can add commands. This guide covers major command families and common flags. To guarantee no installed subcommand is missing, generate the inventory from your own installation using Section 1, then inspect each command's built-in help.

## 1. Exact inventory from your Windows installation

Run in **Windows CMD**:

```cmd
git --version
gh --version
git help -a
git --list-cmds=main,others,ancillary,alias,nohelpers
gh
gh help
gh extension list
```

Save inventories:

```cmd
git help -a > git-all-commands.txt 2>&1
git --list-cmds=main,others,ancillary,alias,nohelpers > git-command-names.txt 2>&1
gh help > gh-command-list.txt 2>&1
gh extension list > gh-extensions.txt 2>&1
```

Get details for any command:

```cmd
git status -h
git log -h
git help log
gh pr --help
gh pr list --help
gh issue list --help
```

For a truly complete per-version option list, run help for each command shown in the inventory. Use `>` to overwrite a file, `>>` to append, and `2>&1` to capture stderr too.

## 2. Important: flags are command-specific

| Command | Meaning |
|---|---|
| `git status -s` | Short status |
| `git status -sb` | Short status plus branch |
| `git log -s` | Suppress patch output (`--no-patch`); often looks like plain `git log` by itself |
| `git log -p` | Show commit patches |
| `git log -S"login"` | Find commits changing the number of occurrences of a string |
| `git log -G"login"` | Find commits whose added/removed lines match a regex |
| `git diff -s` | Suppress diff output; not the same as `git status -s` |
| `git show -s --format=fuller HEAD` | Commit metadata without patch |

Always verify a flag with `git <command> -h`.

## 3. Git command reference

### Setup and repository inspection

| Command | Purpose |
|---|---|
| `git --version` | Git version |
| `git --help` | General help |
| `git help -a` | Available Git commands |
| `git help -g` | Git guides/concepts |
| `git init` | Initialize repository |
| `git clone <URL>` | Clone repository |
| `git status` | Working-tree status |
| `git status -s` | Compact status |
| `git status -sb` | Compact status and branch |
| `git rev-parse --show-toplevel` | Repository root |
| `git rev-parse HEAD` | Current commit ID |
| `git branch --show-current` | Current branch |
| `git log -1 --oneline --decorate` | Latest commit |
| `git ls-files` | Tracked files |
| `git ls-files -o --exclude-standard` | Untracked, non-ignored files |
| `git check-ignore -v <path>` | Show matching ignore rule |
| `git fsck` | Check object connectivity/integrity |
| `git gc` | Repository housekeeping |
| `git archive --format=zip -o source.zip HEAD` | Archive a commit/tree |

### Configuration

| Command | Purpose |
|---|---|
| `git config --list` | List config |
| `git config --list --show-origin` | Config plus source file |
| `git config --get user.name` | Read author name |
| `git config --get user.email` | Read author email |
| `git config --global user.name "Your Name"` | Set global name |
| `git config --global user.email "you@example.com"` | Set global email |
| `git config --local user.name "Your Name"` | Set repo-specific name |
| `git config --global --edit` | Edit global config |
| `git config --local --edit` | Edit repo config |
| `git config --unset <key>` | Remove config key |
| `git help config` | Full config help |

### Staging, diff, and commits

| Command | Purpose |
|---|---|
| `git add <file>` | Stage one file |
| `git add .` | Stage changes under current directory |
| `git add -A` | Stage all additions/modifications/deletions |
| `git add -p` | Stage selected hunks interactively |
| `git add -n <path>` | Dry-run add |
| `git restore --staged <file>` | Unstage but keep working copy |
| `git commit -m "message"` | Commit staged changes |
| `git commit -am "message"` | Commit tracked-file changes; doesn't add untracked files |
| `git commit --amend` | Amend latest commit; rewrites history |
| `git commit --amend --no-edit` | Amend without changing message |
| `git commit --allow-empty -m "message"` | Empty commit |
| `git diff` | Working tree vs index |
| `git diff --cached` | Staged changes vs HEAD |
| `git diff HEAD` | Working tree/index vs HEAD |
| `git diff --stat` | Change summary |
| `git diff --name-only` | Changed paths only |
| `git diff --name-status` | Changed paths and statuses |
| `git diff --check` | Check whitespace errors |
| `git diff --word-diff` | Word-level diff |
| `git show --stat HEAD` | Latest commit summary |
| `git show -s --format=fuller HEAD` | Latest commit metadata |

### History and search

| Command | Purpose |
|---|---|
| `git log` | Commit history |
| `git log --oneline` | Compact history |
| `git log --oneline --decorate --graph --all` | Graph of all refs |
| `git log -n 10` | Latest 10 commits |
| `git log -p -3` | Patches for latest 3 commits |
| `git log -s` | Suppress patch output |
| `git log --stat` | File-change summary per commit |
| `git log --name-only` | Changed filenames |
| `git log --name-status` | Filenames and change type |
| `git log --author="Name"` | Filter author |
| `git log --since="2 weeks ago"` | Filter by date |
| `git log --all --oneline --grep="login"` | Search commit messages |
| `git log -S"login" --oneline` | Search changes in string occurrence count |
| `git log -G"login" --oneline` | Search changed lines by regex |
| `git log -- path/to/file` | File history |
| `git log main..feature --oneline` | Commits on feature not in main |
| `git log feature..origin/feature --oneline` | Remote-only commits relative to local |
| `git log origin/feature..feature --oneline` | Local-only commits relative to remote |
| `git blame <file>` | Last change per line |
| `git shortlog -sn` | Commit counts by author |
| `git grep -n "text" -- "*.cs"` | Search tracked C# files |
| `git show <commit>` | Inspect commit |
| `git show <commit>:<path>` | Show file from commit |
| `git rev-list --count HEAD` | Count reachable commits |
| `git describe --tags --always` | Describe current commit |

### Branches

| Command | Purpose |
|---|---|
| `git branch` | Local branches |
| `git branch -a` | Local and remote-tracking branches |
| `git branch -r` | Remote-tracking branches |
| `git branch -v` | Branches with latest commit |
| `git branch -vv` | Branches with upstream info |
| `git branch --show-current` | Current branch |
| `git branch <name>` | Create branch |
| `git switch <branch>` | Switch branch |
| `git switch -c <branch>` | Create and switch |
| `git switch -` | Previous branch |
| `git checkout <branch>` | Legacy branch switch |
| `git checkout -b <branch>` | Create and switch |
| `git branch -m <new>` | Rename current branch |
| `git branch -m <old> <new>` | Rename branch |
| `git branch -d <branch>` | Delete merged branch |
| `git branch -D <branch>` | Force-delete branch; potentially destructive |
| `git branch --merged` | Branches merged into current |
| `git branch --no-merged` | Branches not merged |
| `git branch --set-upstream-to=origin/<branch>` | Set upstream |
| `git branch --unset-upstream` | Remove upstream |

### Remotes, fetch, pull, push

| Command | Purpose |
|---|---|
| `git remote -v` | Remote URLs |
| `git remote show origin` | Inspect remote/tracking |
| `git remote add origin <URL>` | Add remote |
| `git remote set-url origin <URL>` | Change URL |
| `git remote rename <old> <new>` | Rename remote |
| `git remote remove <name>` | Remove remote config |
| `git fetch` | Download remote updates; no merge |
| `git fetch origin` | Fetch origin |
| `git fetch --all` | Fetch all remotes |
| `git fetch -p` | Prune stale remote-tracking refs |
| `git pull` | Fetch and integrate upstream |
| `git pull --ff-only` | Only fast-forward |
| `git pull --rebase` | Rebase local commits onto fetched upstream |
| `git push` | Push configured branch |
| `git push -u origin <branch>` | Push and set upstream |
| `git push origin HEAD` | Push current branch to same branch name |
| `git push --all` | Push local branches |
| `git push --tags` | Push tags |
| `git push --delete origin <branch>` | Delete remote branch |
| `git push --dry-run` | Preview push |
| `git ls-remote --heads origin` | List remote branches |
| `git ls-remote --tags origin` | List remote tags |

### Merge, rebase, cherry-pick, conflicts

| Command | Purpose |
|---|---|
| `git merge <branch>` | Merge into current branch |
| `git merge --no-ff <branch>` | Force merge commit |
| `git merge --ff-only <branch>` | Fast-forward only |
| `git merge --abort` | Abort merge |
| `git mergetool` | Launch configured merge tool |
| `git rebase <branch>` | Replay commits onto another base |
| `git rebase -i HEAD~3` | Interactive rebase last 3 commits |
| `git rebase --continue` | Continue after resolving conflicts |
| `git rebase --skip` | Skip current patch |
| `git rebase --abort` | Abort rebase |
| `git cherry-pick <commit>` | Apply commit on current branch |
| `git cherry-pick <sha1> <sha2>` | Apply multiple commits |
| `git cherry-pick --continue` | Continue after conflict |
| `git cherry-pick --skip` | Skip current commit |
| `git cherry-pick --abort` | Abort cherry-pick |
| `git diff --name-only --diff-filter=U` | List unresolved conflicts |
| `git add <resolved-file>` | Mark resolved file staged |

### Stash

| Command | Purpose |
|---|---|
| `git stash push -m "message"` | Stash tracked changes with message |
| `git stash push -u -m "message"` | Include untracked files |
| `git stash -u` | Stash tracked and untracked changes |
| `git stash list` | List stashes |
| `git stash show` | Summary of latest stash |
| `git stash show -p` | Patch of latest stash |
| `git stash apply` | Restore and keep stash |
| `git stash apply stash@{1}` | Apply specific stash |
| `git stash pop` | Restore and remove stash if successful |
| `git stash drop stash@{0}` | Delete one stash |
| `git stash clear` | Delete all stashes |
| `git stash branch <new-branch>` | Create branch and apply stash |

### Undo and recovery

| Command | Purpose |
|---|---|
| `git restore <file>` | Discard unstaged changes in file |
| `git restore --staged <file>` | Unstage file |
| `git reset --soft HEAD~1` | Move branch; keep changes staged |
| `git reset --mixed HEAD~1` | Move branch; keep changes unstaged |
| `git reset --hard HEAD~1` | Move branch and discard tracked changes to target |
| `git revert <commit>` | New commit that reverses a commit |
| `git revert --no-edit HEAD` | Revert without editor |
| `git revert --continue` | Continue revert |
| `git revert --abort` | Abort revert |
| `git reflog` | Recent HEAD/ref movements |
| `git reflog --all` | Reflogs for all refs |
| `git clean -n` | Preview untracked-file deletion |
| `git clean -nd` | Preview untracked files/directories |
| `git clean -f` | Delete untracked files |
| `git clean -fd` | Delete untracked files/directories |
| `git clean -fdx` | Also delete ignored files; highly destructive |
| `git fsck --lost-found` | Search unreachable objects |

**Safety:** `reset --hard`, `clean -fd`, `clean -fdx`, and `stash clear` can destroy work. Preview with `git status`, `git diff`, and `git clean -nd`. `git revert` is generally safer for already-shared commits.

### Tags, files, worktrees, submodules, bundles

| Command | Purpose |
|---|---|
| `git tag` | List tags |
| `git tag -l "v1*"` | Filter tags |
| `git tag v1.0` | Lightweight tag |
| `git tag -a v1.0 -m "Version 1.0"` | Annotated tag |
| `git show v1.0` | Inspect tag |
| `git tag -d v1.0` | Delete local tag |
| `git push origin v1.0` | Push one tag |
| `git push origin --delete v1.0` | Delete remote tag |
| `git mv old.txt new.txt` | Move/rename tracked file |
| `git rm file.txt` | Remove tracked file and stage deletion |
| `git rm --cached file.txt` | Stop tracking but keep local file |
| `git worktree list` | List worktrees |
| `git worktree add ../repo-feature feature` | Add worktree |
| `git worktree remove ../repo-feature` | Remove worktree |
| `git worktree prune` | Prune stale worktree metadata |
| `git submodule status` | Show submodule status |
| `git submodule update --init --recursive` | Initialize/update nested submodules |
| `git submodule add <URL> <path>` | Add submodule |
| `git submodule deinit <path>` | Deinitialize submodule |
| `git bundle create repo.bundle --all` | Create bundle of refs |
| `git bundle verify repo.bundle` | Verify bundle |
| `git bundle list-heads repo.bundle` | List refs in bundle |

## 4. GitHub CLI (`gh`) command families

Run `gh` or `gh help` to list commands available in your installed version. Common command families include:

`agent-task`, `alias`, `api`, `attestation`, `auth`, `browse`, `cache`, `codespace`, `completion`, `config`, `copilot`, `discussion`, `extension`, `gist`, `gpg-key`, `issue`, `label`, `org`, `pr`, `preview`, `project`, `release`, `repo`, `ruleset`, `search`, `secret`, `skill`, `ssh-key`, `status`, `variable`, `run`, `workflow`.

Some commands are version-dependent, preview features, permission-dependent, or added by extensions. Use `gh help` for the actual installed list.

### General options (only where supported)

| Option | Purpose |
|---|---|
| `--help` | Help for command |
| `--version` | CLI version |
| `-R OWNER/REPO`, `--repo OWNER/REPO` | Choose repository |
| `--hostname HOST` | Choose GitHub host where supported |
| `--limit 10` | Limit returned items where supported |
| `--json field1,field2` | Select JSON fields |
| `--jq "EXPRESSION"` | Filter JSON using jq |
| `--template "TEMPLATE"` | Format with Go template |
| `--web` | Open in browser where supported |

### Authentication, configuration, aliases, extensions

| Command | Purpose |
|---|---|
| `gh auth login` | Log in |
| `gh auth login --web` | Browser-based login when supported |
| `gh auth status` | Show login status |
| `gh auth logout` | Log out |
| `gh auth switch` | Switch account |
| `gh auth refresh` | Refresh credentials/scopes |
| `gh auth token` | Print token — sensitive, never share it |
| `gh config list` | List config |
| `gh config get editor` | Read editor setting |
| `gh config set editor "notepad"` | Set editor example |
| `gh alias list` | List aliases |
| `gh alias set prs "pr list"` | Create alias |
| `gh extension list` | List installed extensions |
| `gh extension search` | Search extensions |
| `gh extension install OWNER/REPO` | Install extension |
| `gh extension upgrade --all` | Upgrade all extensions |

### Pull requests (`gh pr`)

| Command | Purpose |
|---|---|
| `gh pr list` | List open PRs by default |
| `gh pr list --state all` | All states |
| `gh pr list --state merged` | Merged PRs |
| `gh pr list --state closed` | Closed PRs |
| `gh pr list --limit 10` | Up to 10 PRs |
| `gh pr list --author USERNAME` | Filter by author |
| `gh pr list --assignee USERNAME` | Filter by assignee |
| `gh pr list --base main` | Filter by base branch |
| `gh pr list --head feature-branch` | Filter by head branch |
| `gh pr list --label bug` | Filter by label |
| `gh pr list --search "in:title login"` | Search PR titles |
| `gh pr view 123` | View PR |
| `gh pr view --web` | Open current PR in browser when detected |
| `gh pr create` | Create PR interactively |
| `gh pr create --base main --head feature --title "Login fix" --body "Fixes login"` | Create PR with details |
| `gh pr checkout 123` | Check out PR locally |
| `gh pr diff 123` | Show PR diff |
| `gh pr checks 123` | Show CI checks |
| `gh pr review 123 --approve` | Approve PR |
| `gh pr review 123 --request-changes --body "Please update tests"` | Request changes |
| `gh pr comment 123 --body "Looks good"` | Comment |
| `gh pr merge 123` | Merge PR |
| `gh pr close 123` | Close PR |
| `gh pr reopen 123` | Reopen PR |
| `gh pr ready 123` | Mark draft ready |
| `gh pr status` | Relevant PR status |

Windows CMD example for PR number, state, title, author, and limit:

**Option 1 — custom formatted output** (include `--json` because `--template` requires it):

```cmd
gh pr list --state all --search "in:title login" --limit 10 --json number,state,title,author --template "{{range .}}#{{.number}} | {{.state}} | {{.title}} | Author: {{.author.login}}{{\"\n\"}}{{end}}"
```

**Option 2 — simple default output** (no custom formatting):

```cmd
gh pr list --state all --search "in:title login" --limit 10
```

Check available fields and options for your installed version with:

```cmd
gh pr list --help
gh pr list --json number,title,author,state
```

### Issues (`gh issue`)

| Command | Purpose |
|---|---|
| `gh issue list` | List open issues by default |
| `gh issue list --state all` | All issue states |
| `gh issue list --limit 10` | Limit results |
| `gh issue list --author USERNAME` | Filter by author |
| `gh issue list --assignee USERNAME` | Filter by assignee |
| `gh issue list --label bug` | Filter by label |
| `gh issue list --search "in:title crash"` | Search issue titles |
| `gh issue view 123` | View issue |
| `gh issue create` | Create issue interactively |
| `gh issue comment 123 --body "Update"` | Add comment |
| `gh issue close 123` | Close issue |
| `gh issue reopen 123` | Reopen issue |
| `gh issue status` | Assignment/status overview |

### Repositories, search, and status

| Command | Purpose |
|---|---|
| `gh repo view` | View current repository |
| `gh repo view OWNER/REPO` | View specified repository |
| `gh repo view --web` | Open repository in browser |
| `gh repo list` | List repositories |
| `gh repo list OWNER --limit 50` | List owner's repositories |
| `gh repo clone OWNER/REPO` | Clone repository |
| `gh repo fork OWNER/REPO` | Fork repository |
| `gh repo create` | Create repository |
| `gh repo sync` | Sync fork with upstream |
| `gh browse` | Open repository resource in browser |
| `gh search repos "query"` | Search repositories |
| `gh search issues "query"` | Search issues |
| `gh search prs "query"` | Search PRs |
| `gh search code "query" --repo OWNER/REPO` | Search code where supported |
| `gh status` | GitHub activity/status |

### GitHub Actions

| Command | Purpose |
|---|---|
| `gh workflow list` | List workflows |
| `gh workflow view <workflow>` | View workflow |
| `gh workflow view <workflow> --yaml` | View workflow YAML |
| `gh workflow run <workflow> --ref main` | Trigger workflow dispatch |
| `gh workflow enable <workflow>` | Enable workflow |
| `gh workflow disable <workflow>` | Disable workflow |
| `gh run list --limit 10` | List runs |
| `gh run list --branch main` | Filter by branch |
| `gh run view <run-id>` | View run |
| `gh run view <run-id> --log` | View logs when supported |
| `gh run view <run-id> --log-failed` | Failed-step logs when supported |
| `gh run watch <run-id>` | Watch run |
| `gh run cancel <run-id>` | Cancel run |
| `gh run rerun <run-id>` | Rerun run |
| `gh run download <run-id>` | Download artifacts |

### Releases, gists, projects, and other families

| Command | Purpose |
|---|---|
| `gh release list` | List releases |
| `gh release view` | View latest release |
| `gh release view v1.0.0` | View named release |
| `gh release create v1.0.0` | Create release |
| `gh release download v1.0.0` | Download assets |
| `gh gist list` | List gists |
| `gh gist view <id>` | View gist |
| `gh gist create file.txt` | Create gist |
| `gh project list` | List projects (permissions apply) |
| `gh project view <number>` | View project |
| `gh label list` | List repository labels |
| `gh label create bug --color FF0000` | Create label |
| `gh org list` | List organizations |
| `gh ssh-key list` | List SSH keys |
| `gh gpg-key list` | List GPG keys |
| `gh api repos/OWNER/REPO` | Read repository via API |
| `gh completion -s powershell` | Generate PowerShell completion (not CMD completion) |

## 5. Windows CMD operators and quoting

| Syntax | Meaning |
|---|---|
| `command1 && command2` | Run second only if first succeeds |
| `command1 & command2` | Run second regardless |
| `command > output.txt` | Overwrite stdout file |
| `command >> output.txt` | Append stdout |
| `command > output.txt 2>&1` | Capture stdout and stderr |
| `"text with spaces"` | Quote argument containing spaces |

CMD does not treat single quotes like Bash and does not support Bash command substitution such as `$(...)`. Prefer `gh --template` when formatting output in CMD, or inspect `--json` output separately.

## Undo changes in a file

To undo changes in a file in Git, use the command based on what you want to undo.

| What you want to do | Windows CMD command | Result |
|---|---|---|
| Discard unstaged changes in one file | `git restore <file>` | Restores the file to its staged version, or to the latest commit if no staged changes exist for it |
| Unstage changes but keep the edits | `git restore --staged <file>` | Moves staged changes back to unstaged changes |
| Discard all unstaged changes under the current directory | `git restore .` | Discards unstaged edits under the current directory |
| Discard staged and unstaged changes in one file | `git restore --source=HEAD --staged --worktree <file>` | Restores the file to the latest commit and removes its staged/unstaged changes |
| Restore a file deleted but not staged | `git restore <file>` | Restores the deleted file from the index |
| Restore a file whose deletion is staged | `git restore --source=HEAD --staged --worktree <file>` | Restores the file from the latest commit and unstages the deletion |

Example for one file:

```cmd
git restore Program.cs
```

**Warning:** `git restore` can permanently discard uncommitted edits. Check `git status` and `git diff` first. If you need the edits, save a copy or stash them before restoring.

---

## 6. Safety checklist

Before risky changes:

```cmd
git status
git diff
git diff --cached
git log -5 --oneline --decorate
git branch --show-current
```

Before deleting untracked files:

```cmd
git clean -nd
```

Create a backup branch before complex changes:

```cmd
git branch backup-before-change
```

- `git fetch` downloads; it does not merge.
- `git pull` fetches and integrates.
- `git revert` makes a new commit reversing an earlier commit.
- `git reset --hard` can discard tracked uncommitted work.
- `git stash apply` retains the stash; `git stash pop` removes it after successful application.
- `git branch -d` is safer than `git branch -D`.

## 7. Official documentation

- Git: https://git-scm.com/docs
- GitHub CLI manual: https://cli.github.com/manual/
- GitHub CLI reference: https://docs.github.com/en/github-cli/github-cli/github-cli-reference

**Best practice:** Use Section 1 to capture the exact subcommands installed on your PC, then use built-in help on each subcommand to capture every flag for your installed version. That is the only reliable way to avoid missing version-specific commands.
