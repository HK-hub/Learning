# Git标签功能实现版本发布

[toc]

# 概述

**内容主要**

 讲解如何使用Shell命令创建和操作Git标签，实现版本发布。通读这篇文章大概需要2分钟。

**Git标签简介**

 Git标签（tag）可以针对某一时间点的版本做标记，常用于版本发布，即Git项目到达一定阶段后，可以借助tag，将稳定的代码发布成一个版本。这就解决了分支代码提交因覆盖很难获取前次代码的问题。Git中的标签总是和某次提交（commit）挂钩的，可以说，tag是对某次历史提交的引用，这样该明白tag的含义和用处了吧。

**实现细节**

 在Git中打标签非常简单，用户可以查看所有标签列表和指定标签信息，也可以指定标签获取对应的代码。另外，本文档使用的标签都是附注标签，因为附注标签带有标签名和说明，日后还可以查看标签信息。这就模拟了一个制品库的基本功能。

# 过程

## 切换到需要打标签的分支上，创建一个附注标签



1. 查看当前分支：

   ```
   $ git branch
   * dev
     Master
   ```

2. 切换到master分支：

   ```
   $ git checkout master
   Switched to branch 'master'
   Your branch is up to date with 'origin/master'.
   ```

3. 创建一个附注标签：

   ```
   $ git tag -a -m "release 0.0.1 version" v0.0.1
   ```

4. 查看标签：

   ```
   $ git tag
   v0.0.1
   ```

5. 列出符合模式的标签：

   ```
   $ git tag -l "v1.*"
   v1.0.0
   v1.1.0
   ```

6. 相关参数和更多用法请参考：

   ```
   $ git tag –h
   ```

## 对历史提交打标签，并查看标签详细信息

默认标签是打在最新一次提交（**HEAD**）上的。如果之前版本忘记打标签，中间又经历了多个版本，如何补打？方式是通过指定commit id打标签：

1. 查看历史提交，确定要补打commit的id：

   ```
   $ git log --pretty=oneline --abbrev-commit
   8314f3c (HEAD -> master, origin/master, origin/HEAD) [FIX] 最后一次修改提交时间戳
   80141be [FIX] 修改提交时间戳3
   638e5c6 [FIX] 修改提交时间戳2
   50dbe3c [FIX] 修改提交时间戳1
   95f459a (tag: v0.0.1, origin/dev, dev) Add README.md
   ```

2. 选择的commit是“50dbe3c”，补打标签：

   ```
   $ git tag -a -m "release 1.0.0 version" v1.0.0 50dbe3c
   ```

3. 查看标签详细信息：

   ```
   $ git show v1.0.0
   tag v1.0.0
   Tagger: ah-hui <673350601@qq.com>
   Date:   Mon Aug 27 16:28:23 2018 +0800
   
   release 1.0.0 version
   
   commit 50dbe3c8fc13965ec8df0a179b9502d7f792ea0d (tag: v1.0.0)
   Author: ah-hui <673350601@qq.com>
   Date:   Mon Aug 27 16:21:25 2018 +0800
   
   [FIX] 修改提交时间戳1
   
   diff --git a/README.md b/README.md
   index 8b25206..c89e946 100644
   --- a/README.md
   +++ b/README.md
   @@ -1 +1,2 @@
   -master
   \ No newline at end of file
   +master
   +201808271618
   \ No newline at end of file
   ```

## 发布标签

通常的git push不会将本地标签推送到远程，需要进行显式的操作。

1. 发布指定标签：

   ```
   $ git push origin v0.0.1
   Counting objects: 1, done.
   Writing objects: 100% (1/1), 163 bytes | 163.00 KiB/s, done.
   Total 1 (delta 0), reused 0 (delta 0)
   To http://some.address/me/tag-test.git
    * [new tag]         v0.0.1 -> v0.0.1
   ```

2. 发布全部标签：

   ```
   $ git push origin --tags
   Counting objects: 2, done.
   Delta compression using up to 8 threads.
   Compressing objects: 100% (2/2), done.
   Writing objects: 100% (2/2), 296 bytes | 148.00 KiB/s, done.
   Total 2 (delta 0), reused 0 (delta 0)
   To http://some.address/me/tag-test.git
    * [new tag]         v1.0.0 -> v1.0.0
    * [new tag]         v1.1.0 -> v1.1.0
   ```

## 误打或需要修改标签时，要先删除，再打新标签

1. 删除本地标签：

   ```
   $ git tag -d v1.2.0
   Deleted tag 'v1.2.0' (was 14c2dc6)
   ```

2. 删除远程标签：

   如果标签已经推送到远程，要删除远程标签，就必须先删除本地标签，然后从远程删除：

   - 先删除本地分支：

     ```
     $ git tag -d v1.1.0
     Deleted tag 'v1.1.0' (was 8c33051)
     ```

   - 然后再使用push命令从远程删除：

     ```
     $ git push origin :refs/tags/v1.1.0
     To http://some.address/me/tag-test.git
      - [deleted]         v1.1.0
     ```

## 获取指定标签的代码

首先你需要git clone到代码，然后

1. 你可以直接在当前分支获取指定tag代码（不推荐）：

   ```
   $ git checkout v0.0.1
   Note: checking out 'v0.0.1'.
   
   You are in 'detached HEAD' state. You can look around, make experimental
   changes and commit them, and you can discard any commits you make in this
   state without impacting any branches by performing another checkout.
   
   If you want to create a new branch to retain commits you create, you may
   do so (now or later) by using -b with the checkout command again. 	Example:
   
   git checkout -b <new-branch-name>
   
   HEAD is now at 95f459a... Add README.md
   ```

   看到了温馨的提示：你当前处于“detached HEAD”状态，意思就是你现在就是个快照，不在任何分支上，无法编辑。当你编辑提交最后git push时，将报错：

   ```
   $ git push
   fatal: You are not currently on a branch.
   To push the history leading to the current (detached HEAD) state now, use
   	git push origin HEAD:<name-of-remote-branch>
   ```

   所以，如果你要编辑，就不能在当前分支checkout标签下的代码，而必须创建一个新的本地分支，如下。

2. 创建一个新的本地分支并指定tag获取代码（推荐）：

   ```
   $ git checkout -b new-branch1 v0.0.1
   Switched to a new branch 'new-branch1'
   ```

   这才是正常操作，编辑，提交，一气呵成。