> GIT NOTES
# 1.Getting Started
## 1.7 Getting Help
```
$ git help <verb>
$ git <verb> --help

$ git <verb> -h

eg1: this will open a manpage in your browser about all about `git config`
$ git help config
$ git config --help

eg2: if you just need a quick refresher for current command,use `-h` option
$ git add -h
```

# 2.Git Basics
## 2.2 Recording Changes to the Repository(over)
### Committing Your Changes
> Remember that the commit records the snapshot you set up in your staging area.  Anything you didn’t stage is still sitting there modified; you can do another commit to add it to your history.

### Skipping the Staging Area
```
git commit -a -m"" => git add All  +  git commit -m""
```

### Removing Files

- `git rm` : The `git rm` command does that, and also removes the file from your working directory
- `git rm --cached xx`:   keep the file on your hard drive but not have Git track it anymore

### Moving Files

```
$ git mv file_from file_to
=>  this command is same to 
$ mv README.md README
$ git rm README.md
$ git add README
```

## 2.3 Viewing the Commit History

`git log`的两类操作：

1. 展示log的方式不一样：eg：`git log --oneline`
2. 对输出的log进行筛选：可按照时间、条数，甚至可以针对某个文件夹打印log日志

## 2.4 Undoing Things(撤销)

撤销涉及两种情况：

1. 已提交：要撤销已经提交的修改用`git commit --amend` 会用新提交覆盖之前的提交
2. 未提交的：根据命令行`git status` 即可看到如何撤销
   - 已添加进暂存区的： `git restore --staged文件名`  从暂存区中移除
   - 未添加进暂存区，撤销本地文件的修改：`git restore 文件名`（有的版本是`git checkout 文件名`）

## 2.5 Working with Remotes

>  It’s important to note that the `git fetch` command only downloads the data to your local repository — ***it doesn’t automatically merge it*** with any of your work or modify what you’re currently working on. You have to merge it manually into your work when you’re ready.

### 2.5.1 本地仓库连接远程仓库的增删改查操作：

1. **增**

   `git reomte add 别名 远程地址`
   
2. **删**

   `git remote remove 别名`

3. **改**

   给远程仓库重新起别名 `git remote rename oldShortname newShortname`

4. **查**
  
   - 查看远程仓库列表 `git remote -v`
   - 查看远程列表详细 `git remote show 别名`

### 2.5.2 如何拉取推送修改：

1. 拉取远程仓库：`git fetch 远程仓库别名`
2. 拉取远程仓库并与本地自动合并：`git pull 远程仓库别名`
3. 推送本地修改至远程：`git push 远程仓库别名 远程分支名`

## 2.6 Tagging

### 2.6.1 仓库中某次提交打标签的基本操作

1. **增**

   - `git tag -a v1.4 -m"my version 1.4"` 

     ***注解类型tag***，该类型tag会记录此次tag的详细信息：谁打的tag、什么时间打的tag..

   - `git tag v1.4-1w -m"lightweight tag"` 

     ***轻量类型tag***，不会记录tag的详细信息

   - `git tag -a v1.2 9fcebo2` 

     针对提交历史的某次提交打tag，`9fcebo2` 是提交的checksum（part of it）

2. **查**

   - `git tag --list` 

     查看所有tag的name

   - `git tag --list "v1.2*"` 

     查看tag名以`v1.2`开头的标签名，eg `v1.2.2`

   - `git show v1.4` 

     查看tag的详细信息

3. **删**

   - `git tag -d <tagname>`

     删除tag，注意远程的不会被删掉

   - `git push <remote> :refs/tags/<tagname>`

     删远程仓库tag的方法一，eg: `git push origin :refs/tags/v1.4-lw`

   - `git push origin --delete <tagname>`

     删远程仓库tag的方法二

### 2.6.2 tag提交至远程仓库

- `git push origin v1.5`

  提交本地的指定tag到远程

- `git push origin --tags`

  提交本地所有tag到远程

### 2.6.3 切换到tag所在的commit

- `git checkout v2.0.0`

  切换到 v2.0.0标签 所在的commit,

- `git checkout -b version2 v2.0.0`

  在当前v2.0.0这次提交的commit基础上创建新分支；此命令根据提示敲，git版本不同，命令也可能是`git switch -c version2`

  

## 2.7 Git Aliases

相当于为git命令配置 简短的快捷方式，例如

`$ git config --global alias.ci commit`

之后，你就可以用`git ci` 来替代 `git commit` 了

## 2.8 Summary

we konw now:

- create or clone a respository
- make changes,stage and commit those changes
- view history of changes

we will go on:

- Git`s killer feature --  ***branch***

# 3.Git Branching

## 3.1 Branches in a Nutshell

> Branching means you diverge from the main line of development and continue to do work without messing with that main line
>
> Git can change the way that you develop

- 每一次stage、commit背后，Git都做了些什么？
- 

> A branch in Git is simply a lightweight movable pointer to one of these commits. The default branch name in Git is `master`. As you start making commits, you’re given a `master` branch that points to the last commit you made. Every time you commit, the `master` branch pointer moves forward automatically.

How does Git know what branch you’re currently on? It keeps a special pointer called `HEAD`



### 3.1.1 分支是什么？

> Branching means you diverge from the main line of development and continue to do work without messing with that main line

### 3.1.2 揭秘每一次stage、commit背后故事

***when you stage***：

1. Staging the files computes a checksum for each one

2. stores that version of the file in the Git repository (Git refers to them as *blobs*),

3. adds that checksum to the staging area

***when you create a commit***

1. stores *blobs* as a `tree object`
2. create a  `commit object ` 
3. ``commit object ` 存储着对 `tree object`的引用以及此次提交的其他信息

<img src="assets/commit-and-tree.png" alt="A commit and its tree." style="zoom:50%;" />

### 3.1.3 Git中的branch

> A branch in Git is simply a lightweight ***movable pointer*** to one of these commits. 

如下面的`testing`分支

> How does Git know what branch you’re currently on? It keeps a special pointer called `HEAD` 

`HEAD`指针指向当前所在分支，切换分支，就是将`HEAD`指针指向该分支

<img src="assets/checkout-master.png" alt="HEAD moves when you checkout." style="zoom:50%;" />

### 3.1.4 Git分支的基本操作

1. 创建分支

   `git branch 分支名`

2. 删除分支

   `git branch -d 分支名`

3. 切换分支

   `git checkout 分支名`

4. 创建并切换分支

   `git checkout -b 分支名`

5. 查看各个分支log

   `git log --oneline --decorate --graph --all`

   `git log`默认是查看当期分支下的log

## 3.2 Basic Branching and Merging

两种合并的情况：`Fast-forward`,`merge-commit`

### `Fast-forward` 快进

> when you try to merge one commit with a commit that can be reached by following the first commit’s history, Git simplifies things by ***moving the pointer*** forward because there is no divergent work to merge together — this is called a “fast-forward.”

如下：将`hotfix`分支合并到`master`分支上，只是将`master`分支上的指针向前移了一下。

```console
$ git checkout master
$ git merge hotfix
Updating f42c576..3a0874c
Fast-forward
 index.html | 2 ++
 1 file changed, 2 insertions(+)
```

<img src="assets/image-20200417151200238.png" style="zoom:50%;" />

### `merge commit` 合并提交

> Instead of just moving the branch pointer forward, Git creates a new snapshot that results from this ***three-way merge*** and automatically creates a new commit that points to it. This is referred to as a ***merge commit***, and is special in that it has more than one parent

如下图：将`iss53` 分支合并到`master`分支，三方合并生成一个新的提交（合并提交）。

```console
$ git checkout master
Switched to branch 'master'
$ git merge iss53
Merge made by the 'recursive' strategy.
index.html |    1 +
1 file changed, 1 insertion(+)
```

<img src="assets/image-20200417152627902.png"  style="zoom:50%;" />

> 注意：遇到冲突，要把冲突解决了，再手动 stage、commit

## 3.3 Branch Management

- 查看分支

  `git branch`

- 查看分支并显示每个分支最后一次提交

  ```console
  $ git branch -v
    iss53   93b412c Fix javascript issue
  * master  7a98805 Merge branch 'iss53'
    testing 782fd34 Add scott to the author list in the readme
  ```

- 查看已合并、未合并的分支

  `git branch --merged`

  `git branch --no-merged`			

  
## 3.4 Branching Workflows

  利用分支的两种典型工作流：

  1. `master`分支用来管理稳定发布版本的代码；

     另外搞个平行分支 `develop`用于管理测试版本、不稳定代码

     测试通过,将`develop`分支合并进`master`分支

  2. `topic branch`:为了一个新功能、新想法，你可以临时随心所欲的开新分支。

## 3.5 Remote Branches

> It’s important to note that when you do a fetch that brings down new remote-tracking branches, you don’t automatically have local, editable copies of them. In other words, in this case, you don’t have a new `serverfix` branch — you have only an `origin/serverfix` pointer that you can’t modify

1. 推送本地分支到远程（远程没有该分支）

   ```git push <remote> <branch>```

   例如将本地`serverfix`分支推送到远程，这样远程仓库也有`serverfix`分支了: `git push origin serverfix`

2. 本地拉取远程分支（本地没有该分支）：两步走-先拉取再checkout

   ```console
   $ git fetch origin
   
   $ git checkout -b serverfix origin/serverfix
   ```

   拉取远程`serverfix`分支到本地的`serverfix`分支

3. 将本地已有分支与远程已有分支建立track关系

   如下在本地`serverfix`分支敲这个命令：将当前分支 与 远程仓库的`serverfix`分支建立track关系

   ```console
   $ git branch --set-upstream-to origin/serverfix 
   Branch serverfix set up to track remote branch serverfix from origin.
   
   or 简写成：
   $ git branch -u origin/serverfix
   ```

4. 其他命令

   - 查看本地分支与远程分支的track关系：`git branch -vv`

     ```console
     $ git branch -vv
       iss53     7e424c3 [origin/iss53: ahead 2] Add forgotten brackets
       master    1ae2a45 [origin/master] Deploy index fix
     * serverfix f8674d9 [teamone/server-fix-good: ahead 3, behind 1] This should do it
       testing   5ea463a Try something new
     ```

   - 远程仓库分支列表以及详细

     列表：`git ls-remote <remote>`

     详细：`git remote show <remote>`

   - 删除远程仓库的分支

     `git push origin --delete serverfix`



## 3.6 `Rebasing`

两种合并分支的方式：`merge`,`rebase`

> With the `rebase` command, you can take all the changes that were committed on one branch and replay them on a different branch

###  `rebase`是如何合并分支的

如下图：采用`rebase`方式将`experiment`分支上的内容合并到`master`分支上

![image-20200422105742805](assets/image-20200422105742805.png)

`git rebase master`这个命令幕后都做了什么操作：

1. 先`experiment`分支的修改暂存起来
2. 将`experiment`分支reset到`master`分支的状态
3. 在重置后的`experiment`分支上replay刚刚暂存起来的修改

最后：切换到`master`分支，进行简单的`fast-forward`合并即可

### 为什么要用`rebase`（和`merge`的区别）

如上例中所示，`rebase`的提交历史更干净线性

### 何时(不)使用`rebase`

> In general the way to get the best of both worlds is to `rebase` local changes you’ve made but haven’t shared yet before you push them in order to clean up your story, but never `rebase` anything you’ve pushed somewhere.

- 在本地开发时可以使用，让我们的提交历史更干净易读

- 不要使用`rebase`合并已经push到远程仓库的内容

# 4. Git on the Server

设置一个共享服务器，方便协同开发

如果不想自己架设一个服务器，也可以选择托管服务器，比如`Github`



# 5. Distributed Git (分布式Git)

## 5.1 Distributed Workflows

利用`GIt`进行协作的常见方式：

- 类似`svn`方式：多个用户向一个中央远程仓库提交

- 类似于`Github`：先`fork`，再`pull request`

  > To contribute to that project, you create your own public clone of the project and push your changes to it. Then, you can ***send a request*** to the maintainer of the main project to ***pull in your changes.*** 

- 类似于`linux`那种多个层级，职责分配型的协作方式



## 5.2 Contributing to a Project

如何参与到一个Git项目中？用的`github`,所以本章节略过

## 5.3 Maintaining a Project

如何维持一个项目？略过，待到有需求再来瞅。



> 核心掌握了，接下来就是经常实践，其他章节待到有需求再来瞅

![image-20200426145307241](assets/image-20200426145307241.png)

