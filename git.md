# Git

## Clone

```bash
git clone --recursive https://github.com/wonmean/kb
```

Specify a target folder, in this case `kb-2` in the current working folder.

```bash
git clone --recursive https://github.com/wonmean/kb kb-2
```

## Checkout

```bash
git checkout main
```

Create new branch.

```bash
git checkout -b jira/ticket-description
```

## Pull remote, fast-forward and recursive

```bash
git pull --ff-only --recurse-submodules
```

## Update another branch without switching

```bash
git fetch --recurse-submodules origin main:main
```

## Update staging to the latest

```bash
git pull --rebase origin main
```

## Squash commits

```bash
git rebase -i HEAD~2
```

Select commits to squash by labeling as `s` for squash.  
Clean up the commit message.  
If necessary, force push to remote.

```bash
git push --force
```

Another way to do this is using `reset` and re-committing.

```bash
git reset HEAD~4
git add -A
git commit -m "Squashed commit"
```

## Update submodule

```bash
git submodule update
```

## Reset changes

```bash
git reset
git reset --hard
git checkout HEAD -- <file>
```

## Branches

```bash
git branch
git branch -D jira/ticket-description
```

Renaming branches requires renaming them on remote.
For a local branch, only the first command is needed.

```bash
git branch -m new-name
git push origin old-name new-name
git push origin -u new-name
```

Clean up deleted remote branches.

```bash
git fetch --prune origin
```

## Setup Git

[See key.md](key.md).

Set the default user. Must be coupled with a SSH key to push.

```bash
git config --local user.name <name>
git config --local user.email <email>
```

Remove commands.

```bash
git config --global --unset user.name
git config --global --unset user.email
```

Set vim as the default editor.

```bash
git config --global core.editor "vim"
```

Change remote target, only if necessary.

```bash
git remote set-url origin https://github.com/wonmean/kb.git
```
