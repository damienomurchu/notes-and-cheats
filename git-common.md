# Git common use cases

Assortment of common operations for git.

## Rename a branch

```
# rename branch you're currently on (-m stands for mv)
git branch -m <new-branch-name>

# rename a branch you're not currently on
git branch -m <old-branch-name> <new-branch-name>

# if only changing capitalisation -M needs to be used
git branch -M <new-branch-name>

```

## Compare things

```bash
# commits on branch2 that are not on branch1
git cherry -v branch1 branch2
```
