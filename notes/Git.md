# Git
## Discard all local changes
```
git reset --hard # removes staged and working directory changes

## !! be very careful with these !!
## you may end up deleting what you don't want to
## read comments and manual.
git clean -f -d # remove untracked
git clean -f -x -d # CAUTION: as above but removes ignored files like config.
git clean -fxd :/ # CAUTION: as above, but cleans untracked and ignored files through the entire repo (without :/, the operation affects only the current directory)

```


## clone without blob
```
git clone https://github.com/torvalds/linux.git --filter=blob:none

```

## clone only one branch
```
git clone -b mybranch --single-branch git://sub.domain.com/repo.git

```

## git co files
[] 中括号表示可选
```
git co [branchname] [--] path_to_file
```
当 branchname 省去时 表示当前分支  git co fileA  表示就是用当前分支的 fileA 覆盖现有 fileA
相当于丢弃本地修改

还可以 
git co origin/master — fileA  就是用 master 端的 fileA 内容覆盖了当前 fileA
PS  指定 path_to_file 前最好加上 `--`


## List untracked files
https://stackoverflow.com/questions/3801321/git-list-only-untracked-files-also-custom-commands
```
git ls-files --others --exclude-standard
```


## All conflicts using ours
```
grep -lr '<<<<<<<' . | xargs git checkout --ours
```

grep 的同时忽略某些 dir
```
grep -lr --exclude-dir=node_modules --exclude-dir=dir2 '<<<<<<<' . | grep .json | xargs git checkout --ours
```

## stash one file
```
git checkout stash@{0} -- README.md
```

## Pull another branch
我在分支 B， 但是想要本地的另一个分支 feat 更新
[Pull for another Git branch without switching - Super User](https://superuser.com/questions/163033/pull-for-another-git-branch-without-switching)
```
git fetch origin feat:feat -f
```

## list branch create by myself
```
列出我创建的分支， 并按时间倒序
git for-each-ref --sort=-creatordate --format='%(refname:short)' refs/heads/ | grep -v 'master' | grep -v 'main'
```

```
git branch -r | xargs -L1 git --no-pager show -s --oneline --author="$(git config user.name)"
```


## amend without message
```
git commit --amend --no-edit


git cac --no-edit
```

## unstage folder
```
git rm --cached -r <dir>

```

## git reflog with time

```
git reflog --date=iso

```


## 查看commitID 所属分支
git branch -a --contains CommitID 『ID要填完整的ID 不能是前几位』


## reset hard到分支的最近一次提交

```
git fetch
git reset --hard origin/lottery
```


## You have not concluded your merge (MERGE_HEAD exists)
[git - You have not concluded your merge (MERGE_HEAD exists) - Stack Overflow](https://stackoverflow.com/questions/11646107/you-have-not-concluded-your-merge-merge-head-exists)
```
rm -rf .git/MERGE*
```

## checkout remote branch to local
```
git checkout -b dev(本地分支名称) origin/dev(远程分支名称)

```

## Get current branch name
```
git symbolic-ref --short -q HEAD
```

## delete remote branch
```
git push origin --delete <branchName>

```

## glab
```
glab mr create \

--title "你的MR标题" \

--target-branch feature/aaa \

--label "dev" \

--no-editor \

--description "Merge Request Description"
```


## merge & pull —rebase
rebase 会变基， 也就是 commit 的 parent 会变化。
merge 的话不会，似乎是按照提交的时间顺序把所有的 commit 合并在一起的

``` 
master     first   b                         c   d
时间- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 
another           从 master 切出 bb  cc   
```
最后如果在 another 分支  git merge master 的话， 是把  master 上的内容拉到 another 分支上
merge 后 another 的 commits 为  a, b, bb, cc, c, d, merge xx  into xx  最后多一个 merge xx into xx

![[ce04808569c71311bace6add63192dacc9d66c7e.png]]
如果在 master 分支 merge  another 的话，`git merge another` 就是把 another 上的内容拉到 master 上
![[97afbfb71cab5c42dde6e948428db3b1ebdd0533.png]]



rebase的话， git rebase master  就是以 master 为 base， 然后我在 another 改动全部接在最新的 master 之上。
下面是 git rebase master 后的 another 分支
![[686cc6283f4d5679919ce68a2998e9e95dcf854e.png]]





