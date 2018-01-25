title: Git
date: 2016-03-10 12:54:04
tags: ['Git']
categories: ['Git']
---

## 快速开始

### 简介
Git是一个免费的、开源的分布式版本控制和源代码管理系统，用于敏捷、高效的处理或大或小的项目。 Git 是 Linus Torvalds 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件。

### Git的开发过程
![cmd-markdown-logo](http://f.hiphotos.baidu.com/baike/s%3D220/sign=7252eeae9b504fc2a65fb707d5dce7f0/a71ea8d3fd1f4134ca7667d8251f95cad0c85ed6.jpg)

1. 从服务器上克隆完整的Git仓库（包括代码和版本信息）到单机上；
2. 在自己的电脑上根据不同的开发目的，创建分支，修改代码；
3. 在单机上并且在自己创建的分支上，提交代码；
4. 在单机上合并分支；
5. 把服务器上最新版的代码fetch下来，然后跟自己的主分支合并；
6. 生成merge request（补丁），然后把merge request发送给主开发者；
7. 看主开发者的反馈，如果主开发者发现两个开发者之间有冲突（他们之间可以合作解决的冲突），就会要求他们先解决冲突，然后再由其中一个人提交。如果主开发者自己可以解决，或者没有冲突，就提交这个merge request到主开发者的分支上；
8. 一般开发者之间解决冲突的方法；开发者之间可以使用pull命令解决冲突，解决完冲突之后再向主开发者提交补丁；

### 创建版本库（仓库，英文名repository）

### Generate static files
第一步，创建仓库：
``` bash
$ mkdir "new repository"
$ cd "new repository"
$ pwd
/home/user/"new repository"
```
* pwd命令用于显示当前目录；

第二步，通过git init命令把这个目录变成Git可以管理的仓库：
``` bash
$ git init
```

* 此时，Git就创建好了；并且会告诉你是一个空的仓库，在这个目录下或多一个.git的目录，这个目录是Git来跟踪管理版本库的，一般不要轻易手动改动这个文件的任何内容，以免Git被破坏了；
如果你没有看到.git目录，那是因为这个目录默认是隐藏的，用ls -ah命令就可以看见；

### 查看仓库当前的状态

这个命令可以让我们时刻掌握仓库当前的状态，显示哪些文件进行了修改；
```bash
$ git status
```

git status告诉我们readme.txt文件被修改了，那么使用git diff就可以查看具体修改了什么内容；
```bash
$ git diff readme.txt
```

提交修改和提交新文件是一样的两步,第一步是git add
```bash
$ git add readme.txt
```

这是没有任何输出。在执行第二步git  commit之前，我们可以再次运行git status看看当前仓库的状态；
```bash
$ git commit -m "add the file of readme.txt"
```

在实际工作中，版本控制系统是有命令告诉我们历史记录，在Git中，我们用git log命令查看，而git log命令显示从最近到最远的提交日志：
```bash
$ git log
```

如果嫌输出信息太多，看得眼花缭乱的，可以试试加上--pretty=oneline参数：
```bash
$ git log --pretty=oneline
```

有时候，在工作中我们可能提交了代码之后才发现不需要这次提交，那么我们就可以采用Git回退的机制，回到上一次提交代码的版本；
首先，Git必须知道当前版本是哪个版本，在Git中，用HEAD表示当前版本，也就是最新的提交93d5dae0....d20508b（注意我的提交ID和你的肯定不一样），上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。现在，我们要把当前版本回退到上一个版本，就可以使用git reset命令：
```bash
$ git reset --hard HEAD^
```

如上步骤之后，我们就看不到之前最新的提交了；那么，现在我们想要回到最新的版本，只要上面的命令行窗口还没有被关掉的话，我们就可以找到最新版本提交时的commit ID，于是就可以指定回到未来的某个版本；命令如下：
```bash
$ git reset --hard "commit ID"
```

如果我们在关闭了上面的命令行窗口后，还想要回到未来的版本，那么就需要找到最新版本提交时的commit ID，这样就可以通过git reflog命令来记录你每一次提交时的命令：
```bash
$ git reset --hard "commit ID"
```

### 工作区和暂存区
Git和其他版本控制系统如SVN的一个不同之处就是有暂存区的概念。

1. 工作区（Working Directory）就是你在电脑里能看到的目录，比如"new repository"文件夹就是一个工作区；

2. 版本库（Repository）工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支master，以及指向master的一个指针叫HEAD。

![cmd-markdown-logo](http://www.liaoxuefeng.com/files/attachments/001384907702917346729e9afbf4127b6dfbae9207af016000/0)

往Git版本库里添加的时候，是分两步执行的：
1. 第一步是用git add把文件添加进去，实际上就是把文件修改添加到暂存区；

![cmd-markdown-logo](http://www.liaoxuefeng.com/files/attachments/001384907720458e56751df1c474485b697575073c40ae9000/0)

2. 第二步是用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支。

![cmd-markdown-logo](http://www.liaoxuefeng.com/files/attachments/0013849077337835a877df2d26742b88dd7f56a6ace3ecf000/0)

* 因为我们创建Git版本库时，Git自动为我们创建了唯一一个master分支，所以，现在，git commit就是往master分支上提交更改。而git add命令实际上就是把要提交的所有修改放到暂存区（Stage），然后，执行git commit就可以一次性把暂存区的所有修改提交到分支。

### 撤销修改
如果我们想要让一个已经被修改的文件回到修改之前的状态，那么就可以使用如下命令：
```bash
$ git checkout -- file
```
* 命令git checkout -- file意思就是，把readme.txt文件在工作区的修改全部撤销，这里有两种情况：
1. 一种是readme.txt自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
2. 一种是readme.txt已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。
总之，就是让这个文件回到最近一次git commit或git add时的状态。

Git同样告诉我们，用命令git reset HEAD file可以把暂存区的修改撤销掉（unstage），重新放回工作区：
```bash
$ git reset HEAD readme.txt
```
* git reset命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用HEAD时，表示最新的版本。

如果要丢弃工作去的修改，可以使用命令：
```bahs
$ git checkout -- file
```

### 删除文件
从版本库中删除文件，就可以使用命令git rm，并且用git commit：
```bash
$ git rm file
$ git commit -m "file name"
```

另一种情况是删错了，因为版本库里还有呢，所以可以使用如下的命令，很轻松地把误删的文件恢复到最新版本：
```bash
$ git checkout -- file
```
