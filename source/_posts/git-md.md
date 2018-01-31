---
title: git.md
date: 2017-05-09 10:47:43
tags:
---

### git命令

**查看分支**

```java
// 查看本地所有分支
git branch
// 查看远程所有分支
git branch -r
// 查看所有本地分支和远程分支
git branch -a
```
<!-- More -->

**创建/切换分支**

```java
// 如果存在dev分支，则切换到该分支，否则新建分支
git checkout dev
// 创建本地dev1分支,然后切换到dev1分支
git checkout -b dev1 => git branch dev1 git checkout dev1
// 从远程分支dev创建本地分支dev1
git checkout -b dev1 origin/dev
```

**合并分支**

```java
// 合并dev1分支，这种模式下，删除分支后，会丢掉分支信息，可以用 --no-ff方法
git merge dev1
git merge --no-ff -m "merge with no-ff" dev1
// 如果分支很多，这个分支历史可能就会变得很复杂了，可以使用 rebase，提交的历史会保持线性
git rebase dev1分支
```

**删除分支**

```java
// 删除分支，如果没有完成合并会有提示
git branch -d dev1
// 强删除
git branch -D dev1
  
// 删除远程分支
git push origin --delete dev
or
git push origin :dev
```

**提交分支**

```java
// 这里冒号可以提交到指定分支，上面命令，把提交本地 master 分支到远程的 dev 分支，远程没有dev这个分支，会创建。
git push origin master:dev
// 本地master提交到远程主分支master
git push origin master => git push origin master:master
```

**跟踪远程分支**

从远程分支 checkout 出来的本地分支，称为 跟踪分支 (tracking branch)。跟踪分支是一种和某个远程分支有直接联系的本地分支。在跟踪分支里输入 git pull/push，Git 会自行推断应该向哪个服务器的哪个分支更新/推送数据。

```java
// 建立追踪关系
git branch -u origin/dev master
or
git branch --set-upstream-to origin/dev master
// 查看所有分支跟踪关系
git branch -vv
```

**打Tag**

```java
// 打tag
git tag -a v1.0 -m 'msg'
// 推送tag
git push origin v1.0
```

git push origin <branch> 推送分支

git rebase <branch> 合并分支

git tag -a <tag> -m 'msg' 打tag

git push orign <tag>  推送tag

git branch --set-upstream-to=origin/4.6.0 4.6.0 关连分支