## Setup
You can check your current git config with `git config --list` \
If you have not yet setup git in your command line, check the local_env_setup guides for instructions.

## Temp check
`git log` - show your git history of current branch \
`git log --oneline --graph` - pretty viz \
`git status` - shows the current file index, indicating staged changes, unstaged changes and untracked files.

## Your essential commands
`git pull` - pull down latest updates \
`git add <FILENAME | . | -p>` - stage file(s) & changes. Use `-p` flag to step through changes \
`git commit -m "your commit message"` - commit \
`git push` - push to a connected remote

## Handy 'oops' extras
Try and do this *before* pushing anywhere \
`git commit --amend -m "a better commit message"` - change the message on the most recent commit \
You can add changes to your most recent commit by using `git add <FILENAME | . | -p>` befor running your `--amend` \
If you had already pushed it, next use `git push --force <branch-name>` but try to avoid having to do this.

`git reset --soft HEAD^` - undo your last commit (but the files & changes are still staged) \
`git reset --mixed HEAD^` | `git reset HEAD^` - undo your last commit and unstage files & changes \
`git reset --hard HEAD^` - undo your last commit and delete all of its files & changes from the working tree. Beware!

`git cherry-pick <commitsha>` - apply specific commit to current branch

## Starting a new repo from the command line
`git init`

## Adding a new remote
`git remote add <remote-name> <remote-url>` - for GitHub repos the url will look like: `https://github.com/<username>/<repo>.git` \
The first time you push to a new remote you will need to use `git push -u <remote-name> master`

## Cloning an existing repo
`git clone <remote-url>` - for GitHub repos the url will look like: `https://github.com/<username>/<repo>.git`

## Branches
`git branch` - see all branches. Add `-v` flag for more info. \
`git checkout -b <new-branch-name>` - create and checkout a new branch \
`git branch -D <branch-name-to-delete>` - delete a branch \
`git push --set-upstream <remote-name> <branch-name>` - push to a new branch \
`git checkout master` | `git checkout branch-name` - use `checkout` to switch branches \
[This awesome git branching training tool](https://learngitbranching.js.org/) is well worth spending some time with!

## Merge / Rebase
`git merge <branch-name>` - merges `<branch-name>` into your current branch, joining the histories together \
`git rebase <branch-name>` - rewrite commits on top of another branch's history

## Pull Requests
PRs are more commonly done from the GUI for ease of review and commenting but if you'd like to look at creating them from the command line, look at `git request-pull --help`
