# Git

## Clone
```bash
git clone <url>
```

## Fetch
```bash
git fetch --prune
```

## Checkout
Create new branch using `-b`.
```bash
git checkout staging
git checkout -b <jira/ticket-description>
```

## Pull remote, fast-forward and recursive
```bash
git pull --ff-only
```

## Update another branch without switching
```bash
git fetch origin staging:staging
```

## Rebase & squash commits
```bash
git rebase -i staging
git rebase -i HEAD~2
```
Select commits to squash by labeling as `s` for squash.  
Clean up the commit message.  
If necessary, force push to remote.
```bash
git push --force
```
Another way to do this is using `reset` and re-commiting.  
This is much easier if you have multiple commits to squash.
```bash
git reset HEAD~4
git add -A
git commit -m <commit message>
```

## Reset changes
Use hard reset with caution.
```bash
git reset
git reset --hard
git checkout HEAD -- <file>
```

## Branches
```bash
git branch
git branch -D <jira/ticket-description>
```
Renaming branches requires renaming them on remote.
For a local branch, only the first command is needed.
```bash
git branch -m new-name
git push origin :<old-name> <new-name>
git push origin -u <new-name>
```
Clean up deleted remote branches.
```bash
git fetch --prune origin
```

## Tags
Use annotated tags (`git tag -a -m`), which contains commit information, instead of lightweight tags (`git tag`).  
Clean up local tags.
```bash
git tag -d `git tag` && git fetch --tags --prune
```

Compile the changelog.
```bash
./changelog/changelog.py compile --archive -r 17.1.0
```
```bash
git add . && git commit -m "Update changelog for 17.1.0"
```
```bash
git tag -a 17.1.0 -m "NSA release 17.1.0"
```
```bash
git push origin 17.1.0
```

Delete a tag.
```bash
TAG_NAME="17.1.0-rc1"
git tag -d $TAG_NAME \
&& git push origin --delete $TAG_NAME
```

## Setup Git
Set the default user.  
Must be coupled with a SSH key to push.
```bash
git config --global user.name <user> \
&& git config --global user.email <email>
```
Remove commands.
```bash
git config --global --unset user.name \
&& git config --global --unset user.email
```
Set vim as the default editor.
```bash
git config --global core.editor "vim"
```
Change remote target, only if necessary.
```bash
git remote set-url origin <url>
```

## Git grep and blame
[Reference](https://gist.github.com/lonnen/3101795).
```bash
git grep -E -n $1 | while IFS=: read i j k; do git blame -L $j,$j $i | cat; done
```
