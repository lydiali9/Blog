---
title: forever
date: 2018-05-17 15:00:15
tags: ['node', 'forever']
categories: ['forever']
---

每次上线到外网服务器用node启动的时候，总会遇到一个问题，如果启动的console窗口一旦关闭，那么相应服务也就停止了.然而在外网服务器上会有多个服务同时启动，那么在管理服务的时候就会很混乱， 接下来forever可以很好的解决这个问题。

### 概念

forever是一个简单的命令式nodejs的守护进程，能够启动，停止，重启App应用。forever完全基于命令行操作，在forever进程之下，创建node的子进程，通过monitor监控node子进程的运行情况，一旦文件更新，或者进程挂掉，forever会自动重启node服务器，确保应用正常运行。

### 安装

``` bash
$ npm install -g forever
```

### 查看帮助

``` bash
$ forever -h
```

### forever命令行的中文解释

``` bash
$ start:启动守护进程
$ stop:停止守护进程
$ stopall:停止所有的forever进程
$ restart:重启守护进程
$ restartall:重启所有的foever进程
$ list:列表显示forever进程
$ config:列出所有的用户配置项
$ set <key> <val>: 设置用户配置项
$ clear <key>: 清楚用户配置项
$ logs: 列出所有forever进程的日志
$ logs <script|index>: 显示最新的日志
$ columns add <col>: 自定义指标到forever list
$ columns rm <col>: 删除forever list的指标
$ columns set<cols>: 设置所有的指标到forever list
$ cleanlogs: 删除所有的forever历史日志
```

### 配置参数options

``` bash
$ -m MAX: 运行指定脚本的次数
$ -l LOGFILE: 输出日志到LOGFILE
$ -o OUTFILE: 输出控制台信息到OUTFILE
$ -e ERRFILE: 输出控制台错误在ERRFILE
$ -p PATH: 根目录
$ -c COMMAND: 执行命令，默认是node
$ -a, ?append: 合并日志
$ -f, ?fifo: 流式日志输出
$ -n, ?number: 日志打印行数
$ ?pidFile: pid文件
$ ?sourceDir: 源代码目录
$ ?minUptime: 最小spinn更新时间(ms)
$ ?spinSleepTime: 两次spin间隔时间
$ ?colors: 控制台输出着色
$ ?plain: ?no-colors的别名，控制台输出无色
$ -d, ?debug: debug模式
$ -v, ?verbose: 打印详细输出
$ -s, ?silent: 不打印日志和错误信息
$ -w, ?watch: 监控文件改变
$ ?watchDirectory: 监控顶级目录
$ ?watchIgnore: 通过模式匹配忽略监控
$ -h, ?help: 命令行帮助信息
```

More info: 
* [forever](https://www.npmjs.com/package/forever)
* [官网](https://github.com/foreverjs/forever)

