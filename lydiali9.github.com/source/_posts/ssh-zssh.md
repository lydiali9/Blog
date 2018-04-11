title: Ubuntu SSH ZSSH
date: 2018-04-11 15:32:25
tags: ['ubutun', 'zssh', 'ssh', 'scp']
categories: ['ssh']
---
最近上线一直使用跳板机，导致上传文件变得麻烦；所以总结了一些知识点。希望可以将自己遇到的每个有意义的问题都记录下来。

## 通过跳板机上传下载文件

### 跳板机账号密码

``` bash
IP: 127.0.0.1:22
username: lynn.li
password: lynn
```

### ubuntu登录跳板机

``` bash
$ sudo zssh lynn.li@127.0.0.1
```

### 跳板机登录到远程服务器

```javascript
sudo ssh 127.0.0.2
```

### 在服务器上先cd至相应要放上传文件的目录之后

```javascript
rz -bye               //在远程服务器的相应目录上运行此命令，表示做好接收文件的准备
ctrl+@                //运行上面命令后，会出现一些乱码字符，不要怕，按此组合键，进入zssh
zssh >                //这里切换到了本地机器
zssh > pwd            //看一下本地机器的目录在那
zssh > ls             //看一下有那些文件
zssh > sz 123.txt     //上传本地机器的当前目录的123。txt到远程机器的当前目录
```

### 下载服务器文件到本地

```javascript
sz filename           //在远程机器上，启动sz， 准备发送文件
                      //看到一堆乱码，不要怕，这会按下组合键
ctrl+@
zssh > pwd            //看看在那个目录，cd 切换到合适的目录
zssh > rz -bye        //接住对应的文件
```


## Ubuntu上传下载远程服务器的文件

### 安装SSH(Secure Shell)服务以提供远程管理服务

```javascript
sudo apt-get install ssh
```

### SSH远程登入Ubuntu机

```javascript
ssh username@192.168.0.1
```

### 将文件/文件夹从远程Ubuntu机拷至本地(scp)

```javascript
sudo scp -r 127.0.0.2:/usr/local/back-end/node_modules.zip
```

### 将文件/文件夹从本地拷至远程 Ubuntu机(scp)

```javascript
sudo scp -r /home/local/Projects/text.html 127.0.0.2:/usr/local/back-end/
```

