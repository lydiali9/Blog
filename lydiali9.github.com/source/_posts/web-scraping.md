---
title: 'web-scraping'
date: 2018-05-18 16:41:23
tags: ['web-scraping']
categories: ['web-scraping', 'cheerio', 'request', 'nodeJS']
---

Before web based API's became the prominent way of sharing data between services we had web scraping. Web scraping is a technique in data extraction where you pull information from websites.

The technologies we will be using to accomplish this are:
* [Node](https://nodejs.org/en/)
* [ExpressJS](https://expressjs.com/):The Node framework that everyone uses and loves.
* [Request](https://github.com/request/request): Helps us make HTTP calls
* [Cheerio](https://github.com/cheeriojs/cheerio): Implementation of core jQuery specifically for the server (helps us traverse the DOM and extract data)

## Setup

Our setup will be pretty simple. If you're already familiar with NodeJS, go ahead and setup your project and include Express, Request and Cheerio as your dependencies.

### Install request and cheerio

``` bash
$ npm install request --save
$ npm install cheerio
```

or `npm install`

```bash
{
  "name": "servicedelivery",
  "version": "1.0.0",
  "description": "fetch some important information from html",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "lynn li",
  "license": "ISC",
  "dependencies": {
    "async": "^2.6.0",
    "cheerio": "^1.0.0-rc.2",
    "express": "^4.16.3",
    "lodash": "^4.17.5",
    "node-spider": "^1.4.1",
    "phantom": "^4.0.12",
    "request": "^2.85.0",
    "request-promise": "^4.2.2",
    "url-parse": "^1.3.0"
  }
}
```


## Usage

### Example 1

``` bash
var https    = require('https');
var fs      = require('fs');
var cheerio = require('cheerio');
var request = require('request');

var url = "https://www.baidu.com/s?wd=72509979849";

function fetchPage(url) {
    startRequest(url);
}

function startRequest(url) {
    https.get(url, function (res) {
        var html = '';
        var titles = [];
        res.setEncoding('utf-8');

        res.on('data', function (chunk) {
            html += chunk;
        });

        res.on('end', function () {

            var $ = cheerio.load(html);
            console.log(html);
        });

    }).on('error', function (err) {
        console.log(err);
    });
}
fetchPage(url);
```

### Example 2

```bash
var express = require('express');
var fs      = require('fs');
var cheerio = require('cheerio');
var request = require('request');
var app     = express();

app.get('/scrape', function(req, res){
    let url = "https://www.baidu.com/s?wd=72509979849";
    request(url, function(error, response, html){

        if(!error){
            var $ = cheerio.load(html);
            console.log(html);
        }
    })
})

app.listen('8081');
exports = module.exports = app;
```

### Example 3

```bash
var request = require('request');
var cheerio = require('cheerio');
var rp = require('request-promise');

var url = "https://www.baidu.com/s?wd=72509979849";
var options = {
    method: "get",
    uri: url,
    body: {
        nu: '72509979849'
    },
    headers: {
        'User-Agent': 'Request-Promise',
        'content-type': 'application/json;charset=UTF-8'
    },
    json: true
}

rp(options)
    .then(function (repos) {
        console.log(repos);
    })
    .catch(function (err) {
        // API call failed...
    });
```

## 抓包工具
　　我们使用Linux服务器，有些时候需要抓取其中的数据包进行分析攻击的类型以及特征，这样就可以根据特征在防火墙上面进行拦截防护了，在Linux的命令行里边我们需要借助tcpdump软件进行抓包.

### Install tcpdump
```bash
yum install -y tcpdump

tcpdump --help
```

More info: 
* [wireshark](https://jingyan.baidu.com/article/c35dbcb0866b698916fcbc81.html)
* [PhantomJS](http://phantomjs.org/quick-start.html)
* [How to use PhantomJS with Node.js](https://ourcodeworld.com/articles/read/379/how-to-use-phantomjs-with-node-js)
* [node-crawler](https://github.com/bda-research/node-crawler)
* [puppeteer 1](https://github.com/GoogleChrome/puppeteer)
* [puppeteer 2](https://developers.google.com/web/tools/puppeteer/articles/ssr)