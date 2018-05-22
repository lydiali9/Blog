---
title: async waterfall
date: 2018-05-22 13:50:57
tags: ['async', 'waterfall']
categories: ['async', 'waterfall', 'nodeJS', 'callback', 'parallel', 'eachSeries']
---

async作为nodeJS开发常用知名第三方类库是学习node的历程中不可缺少的一部分.

![node知识框架图](https://github.com/lydiali9/Blog/blob/master/lydiali9.github.com/images/node.png?raw=true)

## 安装

```javascript
npm install --save async
```

## 引用

```javascript
import waterfall from 'async/waterfall';
```

## 常用方法简介

### async.waterfall

waterfall(tasks, callbackopt)

>瀑布流函数，串行执行数组中的每一个函数最后执行回调。
第一个参数tasks是一个数组，数组包含的是需要依次执行的函数名。
第二个参数为回调函数，当瀑布流函数执行出现错误时会执行这个回调函数并将错误信息返回，当瀑布流函数无错误时，会在执行完tasks数组中包含的函数后执行这个回调函数。

async最好用的流程控制方法，可大大降低代码耦合度。（一个函数只做一件事，async.waterfall则实现了一系列函数的异步组合）

* Example 1

```javascript
async.waterfall([
    myFirstFunction,
    mySecondFunction,
    myLastFunction,
], function (err, result) {
    // result now equals 'done'
});
function myFirstFunction(callback) {
    callback(null, 'one', 'two');
}
function mySecondFunction(arg1, arg2, callback) {
    // arg1 now equals 'one' and arg2 now equals 'two'
    callback(null, 'three');
}
function myLastFunction(arg1, callback) {
    // arg1 now equals 'three'
    callback(null, 'done');
}
```

* Example 2

```javascript
async.waterfall([
    function(callback) {
        callback(null, 'one', 'two');
    },
    function(arg1, arg2, callback) {
        // arg1 now equals 'one' and arg2 now equals 'two'
        callback(null, 'three');
    },
    function(arg1, callback) {
        // arg1 now equals 'three'
        callback(null, 'done');
    }
], function (err, result) {
    // result now equals 'done'
});
```

### async.parallel

async.parallel(tasks,callback)

> tasks并行运行函数集合，而不必等到上一个函数完成。如果任何函数发生错误，会立刻执行回调函数，并返回错误信息；若没有发生错误，则会在所有tasks函数执行完毕之后用回调函数将结果返回。

* Example

```javascript
async.parallel([
    function(callback) {
        setTimeout(function() {
            callback(null, 'one');
        }, 200);
    },
    function(callback) {
        setTimeout(function() {
            callback(null, 'two');
        }, 100);
    }
],
// optional callback
function(err, results) {
    // the results array will equal ['one','two'] even though
    // the second function had a shorter timeout.
});
```

More info: 
[async](https://caolan.github.io/async/docs.html#)
[node面试](https://github.com/jimuyouyou/node-interview-questions)