

`git rebase origin`  vs `git rebase origin/main`


`git rebase origin` means "rebase from the tracking branch of `origin`", while `git rebase origin/master` means "rebase from the branch `master` of `origin`"

`pull = fetch + merge`

`pull -rebase= fetch + rebase`


type of git merge

- Merge: A standard merge will take each commit in the branch being merged and add them to the history of the base branch based on the timestamp of when they were created.
- Fast forward merge: If we change our example so **no new commits** were made to the base branch since our branch was created, Git can do something called a “Fast Forward Merge”. This is the same as a Merge but **does not** create a merge commit.
- Squash and merge: created merge commits
- Rebase and Merge: 


Compare branch

git diff branch1...branch2



`git log --oneline --graph --decorate --abbrev-commit branch1..branch2`


Reflog

It is gits safetly system .It records almost every change you make in your repository. Think of this as a chronological history of everything you've done in your local repo.


git rev-list --all

git rev-list --branches


## Recover a deleted branch using Git Reflog


1 get all history

`git reflog`

```
➜  chhano__web git:(dev) ✗ git reflog
71ad81f (HEAD -> dev, origin/dev) HEAD@{0}: checkout: moving from reflog-demo to dev
cc61763 HEAD@{1}: commit: hello
71ad81f (HEAD -> dev, origin/dev) HEAD@{2}: checkout: moving from dev to reflog-demo
71ad81f (HEAD -> dev, origin/dev) HEAD@{3}: rebase (finish): returning to refs/heads/dev
71ad81f (HEAD -> dev, origin/dev) HEAD@{4}: rebase (start): checkout HEAD~3
```


2 get history stamp


3 recover
`git checkout -b reflog-demo HEAD@{5}`


`git blame .github/workflows/node-lint.yml`



Blame
``**git config** --global alias.who blame`



`**git reset --hard HEAD^**` command it will only revert the top commit means the latest commit.

`**git reset --hard HEAD^**` command it will only revert the top commit means the latest commit.


Git reset

-   **`--soft`**: **uncommit** changes, changes are left staged (_index_).
-   **`--mixed`** _(default)_: **uncommit + unstage** changes, changes are left in _working tree_.
-   **`--hard`**: **uncommit + unstage + delete** changes, nothing left.

`git reflog` +`git cherrypick`


git reset --hard <my-branch-tip-before-rebase>


Rebase from one branch from master to to develop

`git cherry-pick -x Commit ID from master`

`git cherry-pick --continue`

