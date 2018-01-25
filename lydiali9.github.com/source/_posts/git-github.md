title: Git与GitHub
date: 2016-04-10 16:19:00
tags: ['GitHub']
categories: ['Git']
---

## 快速开始

### 简介
GitHub是开源代码库以及版本控制系统，Github拥有140多万开发者用户。随着越来越多的应用程序转移到了云上，Github已经成为了管理软件开发以及发现已有代码的首选方法。作为一个分布式的版本控制系统，在Git中并不存在主库这样的概念，每一份复制出的库都可以独立使用，任何两个库之间的不一致之处都可以进行合并。GitHub可以托管各种git库，并提供一个web界面；


### 创建远程库
首先注册GitHub账号。由于本地Git仓库和GitHub仓库之间的传输是通过SSH加密的，所以，需要一点设置：

第1步：创建SSH Key。在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有id_rsa和id_rsa.pub这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下打开Git Bash），创建SSH Key：
```bash
$ ssh-keygen -t rsa -C "youremail@example.com"
```
这里需要把邮箱地址换成自己的邮箱地址，然后一路回车，使用默认值即可；
如果一切顺利的话，可以在用户主目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人。

第2步：登陆GitHub，打开“Account settings”，“SSH Keys”页面：
然后，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容：
![cmd-markdown-logo](http://www.liaoxuefeng.com/files/attachments/001384908342205cc1234dfe1b541ff88b90b44b30360da000/0)

点“Add Key”，你就应该看到已经添加的Key：
![cmd-markdown-logo](http://www.liaoxuefeng.com/files/attachments/0013849083502905a4caa2dc6984acd8e39aa5ae5ad6c83000/0)

因为GitHub需要识别出你推送的提交确实是你推送的，而不是别人冒充的，而Git支持SSH协议，所以，GitHub只要知道了你的公钥，就可以确认只有你自己才能推送。

当然，GitHub允许你添加多个Key。假定你有若干电脑，你一会儿在公司提交，一会儿在家里提交，只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了。

在GitHub上免费托管的Git仓库，任何人都可以看到喔（但只有你自己才能改）。所以，不要把敏感信息放进去。如果你不想让别人看到Git库，有两个办法，一个是交点保护费，让GitHub把公开的仓库变成私有的，这样别人就看不见了（不可读更不可写）。另一个办法是自己动手，搭一个Git服务器，因为是你自己的Git服务器，所以别人也是看不见的。

### 添加远程库
本地创建了一个Git仓库后，又想在GitHub创建一个Git仓库，并且让这两个仓库进行远程同步，这样，GitHub上的仓库既可以作为备份，又可以让其他人通过该仓库来协作，真是一举多得。

首先，登陆GitHub，然后，在右上角找到“Create a new repo”按钮，创建一个新的仓库：
![cmd-markdown-logo](http://www.liaoxuefeng.com/files/attachments/0013849084639042e9b7d8d927140dba47c13e76fe5f0d6000/0)

在Repository name填入"new repository"，其他保持默认设置，点击“Create repository”按钮，就成功地创建了一个新的Git仓库：
![cmd-markdown-logo](http://www.liaoxuefeng.com/files/attachments/0013849084720379a3eae576b9f417da2add578c8612a2e000/0)

目前，在GitHub上的这个"new repository"仓库还是空的，GitHub告诉我们，可以从这个仓库克隆出新的仓库，也可以把一个已有的本地仓库与之关联，然后，把本地仓库的内容推送到GitHub仓库。

将远程的库拉渠道本地，命令如下：
```bash
$ git remote add origin git@github.com:michaelliao/learngit.git
```
下一步，就可以把本地库的所有内容推送到远程库上：
```bash
$ git push -u origin master
```
把本地库的内容推送到远程，用git push命令，实际上是把当前分支master推送到远程。
把本地master分支的最新修改推送至GitHub，现在，你就拥有了真正的分布式版本库！

### 创建与合并分支
创建新的分支，并且切换到新的分支上:
```bash
$ git checkout -b "new branch"
```

git checkout命令加上-b参数表示创建并切换，相当于以下两条命令：
```bash
$ git branch "new branch"
$ git checkout "new branch"
```

然后，用git branch命令查看当前分支：
```bash
$ git branch
```
git branch命令会列出所有分支，当前分支前面会标一个*号。

现在，我们就可以把新创建的分支合并到新的分支上
```bash
$ git merge "new branch"
```
git merge命令用于合并指定分支到当前分支;

合并完成之后，我们就可以删除刚才创建的新的分支了
```bash
$ git branch -d dev
```

### 解决冲突
首先准备新的分支，并且在新的分支上修改文件，并且提交代码；
然后切换到master主分支上，在master上也修改刚才修改的文件，并且提交代码到master分支上；这种情况下，Git无法执行快速合并，但是这种合并往往会产生冲突；
我们可以根据提示，找到冲突的文件；然后根据具体的逻辑解决冲突，并且再一次进行提交代码；最后我们就可以用参数的git log看到分支的合并情况：
```bash
$ git log --graph --pretty=oneline --abbrev-commit
```
最后删除新创建的分支；





