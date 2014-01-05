# Git

## Working with remote branches

#### list all branches including remote branches

```bash
git branch -a
```

#### checkout a remote branch

```bash
git checkout -b 28605_tooling origin/28605_tooling
```

## Cancelling changes

* [http://stackoverflow.com/questions/1146973/how-do-i-revert-all-local-changes-in-a-git-managed-project-to-previous-state](http://stackoverflow.com/questions/1146973/how-do-i-revert-all-local-changes-in-a-git-managed-project-to-previous-state)

### Revert changes made to the working copy

```bash
git checkout .
```

### Revert changes made to the index(ie. that you have added)

```bash
git reset
```

### Revert a change you have committed

```bash
git revert ...
```

### Remove untracked files from the working directory

```bash
git clean -f
```

To delete directories as well

```bash
git clean -f -d
```