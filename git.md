# Git notes

# Common uses
### Diff version of same file from 2 different branches
`git diff mybranch master -- myfile.cs`



### books

- Git Internals, source code control and beyond. Scott Chacon. Peep Code press


### notes

- `git add -i ` #interactive adding
- `git ls -tree` #SHA-1s of all the blobs and trees it points to
- `git grep` #grep entire repo
- `git diff` #can also generate patch files
- conflicts; if we branch our code and then edit the same place in a file in different ways in each branch
- `git-archive` #create a tar or zip of current working directory
- `git gc` #git garbage collector; packs your objects into the delta-compressed format, saving  a lot of space and seriously speeding up several commands
- `git fsck` #git health check of repo
- `git prune` #Removes objects that are no longer pointed to
- `git remote show <remote>` #details of a remote
- `git-daemon` #background daemon to update repos w/ fetches
- `git clone -o <remote-name> <remote-link>` #clone repo & name remote as <remote-name> instead of origin
- 
