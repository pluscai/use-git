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

![A commit and its tree.](https://git-scm.com/book/en/v2/images/commit-and-tree.png)

### 3.1.3 Git中的branch

> A branch in Git is simply a lightweight ***movable pointer*** to one of these commits. 

如下面的`testing`分支

> How does Git know what branch you’re currently on? It keeps a special pointer called `HEAD` 

`HEAD`指针指向当前所在分支，切换分支，就是将`HEAD`指针指向该分支

![HEAD moves when you checkout.](D:\csx\learn-git\assets\checkout-master.png)

### 3.1.4 Git分支的基本操作

1. 创建分支

   `git branch 分支名`

2. 切换分支

   `git checkout 分支名`

3. 创建并切换分支

   `git checkout -b 分支名`

4. 查看各个分支log

   `git log --oneline --decorate --graph --all`

   `git log`默认是查看当期分支下的log

   