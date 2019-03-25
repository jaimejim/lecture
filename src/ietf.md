# Github general usage notes

Started to do version control on linux when they were told to pay. Linus created `git`. Github and gitlab are the most popular. It is used for social coding.`

## Working with repos

**`git clone <url.git>`** clone an exsisting repository into a new directory.

`git init` create an empty Git repository or reinitialize an existing one.

## Staging

`git status` shows modified files in working directory, staged for your next commit

**`git add [file]`** add a file as it looks now to your next commit (stage).

`git reset [file]` unstage a file while retaining the changes in working directory.

`git diff` diff of what is changed but not staged.

`git diff --staged` diff of what is staged but not yet committed.

**`git commit -m "[descriptive message]"`** commit your staged content as a new commit snapshot.

## Branch Merge

`git branch` list your branches. a * will appear next to the currently active branch.

**`git branch [branch-name]`** create a new branch at the current commit.

`git checkout` switch to another branch and check it out into your working directory.

**`git merge [branch]`** merge the specified branch’s history into the current one.

`git log` show all commits in the current branch’s history.

## Share and Update

`git remote add [alias] [url]` add a git URL as an alias.

`git fetch [alias]` fetch down all the branches from that Git remote.

`git merge [alias]/[branch]` merge a remote branch into your current branch to bring it up to date.

**`git push [alias] [branch]`** transmit local branch commits to the remote repository branch.

**`git pull`** fetch and merge any commits from the tracking remote branch.

## Rewrite History

`git rebase [branch]` apply any commits of current branch ahead of specified one.

`git reset --hard [commit]` clear staging area, rewrite working tree from specified commit.

## Temporary Commits

**`git stash`** save modified and staged changes.

`git stash list` list stack-order of stashed file changes

`git stash drop` discard  the changes from top of stash stack.

## Useful Aliases

**`alias gs='git status'`** this one is used a lot.

`alias gcb='git rev-parse --abbrev-ref HEAD | pbcopy'` copy the current branch name.

**`tig`** for tracking changes on your terminal.