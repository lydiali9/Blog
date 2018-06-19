---
title: token based authentication - JSON Web Tokens
date: 2018-06-19 15:43:56
tags: ['token', 'authentication']
categories: ['authentication']
---

I have created my first project width node, express and mongoose in those days, There is an important part for project which is authentication. So here is my summary in the following.

## Quick Start

### Install

``` bash
npm install jsonwebtoken --save
npm install bcryptjs --save
```
* express is the popular Node framework
* mongoose is how we interact with our MongoDB database
* morgan will log requests to the console so we can see what is happening
* body-parser will let us get parameters from our POST requests
* jsonwebtoken is how we create and verify our JSON Web Tokens

### Config File(config.js)

``` bash
module.exports = {
    'secret': 'ilovescotchyscotch',
};
```

### App.js

``` bash
const express = require("express");
const config = require("config-lite");
const cookieParser = require("cookie-parser");
const session = require("express-session");
const connectMongo = require("connect-mongo");
const winston = require("winston");
const expressWinston = require("express-winston");
const history = require("connect-history-api-fallback");
const chalk = require("chalk");
const bodyParser = require('body-parser');
const jwt = require('jsonwebtoken');

const constant = require('./utils/constant');
const db = require("./mongodb/db.js");
const router = require("./routes/index.js");

const app = express();

app.all('*', (req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Content-Type, Authorization, X-Requested-With, x-access-token, UserId");
    res.header("Access-Control-Allow-Methods", "PUT,POST,GET,DELETE,OPTIONS");
    res.header("Access-Control-Allow-Credentials", true); //可以带cookies
    res.header("Content-Type", "application/json;charset=utf-8");
    res.header("X-Powered-By", '3.2.1');
    if (req.method == 'OPTIONS') {
        res.send(200);
    } else {
        next();
    }
});

app.set('superSecret', constant.TOKEN_SECRET);

app.use(bodyParser.json({limit: '50mb'}));
app.use(bodyParser.urlencoded({limit: '50mb', extended: true}));

// app.use(Statistic.apiRecord)
const MongoStore = connectMongo(session);
app.use(cookieParser());
app.use(session({
    name: config.session.name,
    secret: config.session.secret,
    resave: false,
    saveUninitialized: true,
    cookie: config.session.cookie,
    store: new MongoStore({
        url: config.url
    })
}))

/*app.use(expressWinston.logger({
    transports: [
        new (winston.transports.Console)({
          json: true,
          colorize: true
        }),
        new winston.transports.File({
          filename: 'logs/success.log'
        })
    ]
}));*/

//router(app);
app.use(router);

/*app.use(expressWinston.errorLogger({
    transports: [
        new winston.transports.Console({
          json: true,
          colorize: true
        }),
        new winston.transports.File({
          filename: 'logs/error.log'
        })
    ]
}));*/

app.use(history());
app.listen(config.port, () => {
    console.log(
        chalk.green(`成功监听端口：${config.port}`)
    )
});
```

### Mongoose(config/default.js)

``` bash
module.exports = {
    port: 8081,
    url: 'mongodb://localhost:27017/pie_media',
    session: {
        name: 'SID',
        secret: 'SID',
        cookie: {
            httpOnly: true,
            secure:   true,
            maxAge:   365 * 24 * 60 * 60 * 1000,
        }
    }
}
```

### Connect mongoose(db.js)

```bash

const mongoose = require("mongoose");
const config = require("config-lite");
const chalk = require("chalk");

mongoose.connect(config.url, {useMongoClient:true});
mongoose.Promise = global.Promise;

const db = mongoose.connection;

db.once('open' ,() => {
    console.log(
    chalk.green('连接数据库成功')
  );
})

db.on('error', function(error) {
    console.error(
      chalk.red('Error in MongoDb connection: ' + error)
    );
    mongoose.disconnect();
});

db.on('close', function() {
    console.log(
      chalk.red('数据库断开，重新连接数据库')
    );
    mongoose.connect(config.url, {server:{auto_reconnect:true}});
});

// export default db;
module.exports = db;
```

### Creating a Sample module
```bash
let mongoose = require('mongoose');

const Schema = mongoose.Schema;

const userSchema = new Schema({
    name: {type: String, default: ''},
    id: Number,
    password: {type: String, default: ''},
    type: {type: Number, default: 0},
    group: [],
    server_limit: [],
    desc: {type: String, default: ''},
    nickname: {type: String, default: ''},
    age: {type: Number, min: 13, max: 80, default: -1}, // -1 unknown
    sex: {type: Number, default: 0},
    email: {type: String, default: ''},
    id_crad: {type: String, default: ''},
    home_address: {type: String, default: ''},
    company_address: {type: String, default: ''},
    create_time: Date,
    update_time: { type: Date, default: Date.now },
    headphoto: {type: String, default: ''},
    number: {type: String, default: ''},
    wechat: {type: String, default: ''},
    weibo: {type: String, default: ''},
    QQ: {type: String, default: ''},
    is_deleted: {type: Number, default: 0}
})

userSchema.index({id: 1});
const User = mongoose.model('User', userSchema);

module.exports = User

```

### Create verifyToken.js
```bash
var jwt = require('jsonwebtoken');
const config = require("./constant");
let $logger = require('./logger');
let $code = require('./code');

function verifyToken(req, res, next) {
    var token = req.body.token || req.query.token || req.headers['x-access-token'];

    if (!token) {
        $logger.error('method: verify_token, msg: No token provided');
        res.json({code: $code.NO_TOKEN, msg: "No token provided", data: ""});
    } else {
        jwt.verify(token, config.TOKEN_SECRET, function (err, decoded) {
            if (err) {
                $logger.error('method: verify_token, msg: No token provided');
                res.json({code: $code.NO_AUTHENTICATE, msg: "Failed to authenticate token", data: ""});
            }

            // if everything good, save to request for use in other routes
            req.decoded = decoded;
            next();
        });
    }
}
module.exports = verifyToken;
```

### Add router
```bash
let express = require('express');
let $api = require('../api');

let $user = require('../controller/user/userManager');
let verifyToken = require('../utils/verifyToken');

let router = express.Router();

// user
router.post($api.login, $user.login);
router.get($api.get_user, verifyToken, $user.getUser);
router.post($api.update_user, verifyToken, $user.updateUser);

module.exports = router;
```

### Create controller(User.js)
```bash
/**
 * crawler
 * @type {es}
 * Created by yuhua.li
 */
var request_promise = require('request-promise');
let $url = require('../../utils/urls');
let $utils = require('../../utils/utils');
let $logger = require('../../utils/logger');
let $code = require('../../utils/code');
var UserModel = require('../../models/user/user');
const CONFIG = require("../../utils/constant");

function createUser(number) {
    return new Promise(function (resolve, reject) {
        UserModel.create({"number": number, nickname: '派娱乐User'}, function (err, user) {
            if (err) {
                $logger.error('method: get_code, msg: 获取验证码成功后，保存用户信息失败');
                reject(err);
            } else {
                // generate a token
                let token = $utils.generateToken(user._id);
                console.log(token);
                let doc = user._doc ? user._doc : {};
                resolve(Object.assign(doc, {token: token}));
            }
        });
    });
}

module.exports = {

    login(req, res) {
        let body = req.body;

        if(body.number && body.code) {
            let url = $url.register;
            let timetamp = new Date().getTime();

            let options = {
                "uri": url,
                "qs": {
                    "tk": $utils.createToken(body.number, timetamp),
                    "product_id": CONFIG.PRODUCT_SDK_ID,
                    "phone_num": body.number,
                    "verification_code": body.code,
                    "timestamp": timetamp,
                    "ver": CONFIG.CMS_VERSION
                },
                "headers": {
                    'User-Agent': 'Request-Promise',
                    'content-type': 'application/json;charset=UTF-8'
                }
            };

            request_promise(options)
                .then(data => {
                    let result = JSON.parse(data);

                    if(result.code == 200) {
                        UserModel.findOne({number: body.number}, function(err, user) {
                            if(!user) {
                                createUser(body.number)
                                    .then(doc => {
                                        res.json({code: $code.SUCCESS, msg: 'success', content: doc});
                                    })
                                    .catch(function (err) {
                                        $logger.error('method: login create_user, msg: ' + err.message);
                                        res.json({code: $code.FALSE, msg: 'failed', err: err.message});
                                    });
                            } else {
                                let token = $utils.generateToken(user._id);
                                let doc = user._doc ? user._doc : {};
                                res.json({code: $code.SUCCESS, msg: 'success', content: Object.assign(doc, {token: token})});
                            }
                        });
                    } else {
                        $logger.error('method: login, msg: ' + result.message);
                        res.json({code: $code.QUERY_MYSQL_FALSE, errmsg: result.message, data: ""});
                    }
                })
                .catch(function (err) {
                    $logger.error('method: login, msg: ' + err.message);
                    res.json({code: $code.FALSE, msg: 'failed', err: err.message});
                });
        } else {
            $logger.error('method: login, msg: 请求信息不完整');
            res.json({code: $code.QUERY_MYSQL_FALSE, msg: "请求信息不完整", data: ""});
        }
    }
```

### Create generateToken.js
```bash
generateToken: function(userId) {
    let token = jwt.sign({ id: userId }, CONFIG.TOKEN_SECRET, {
        expiresIn: 365 * 24 * 60 * 60 * 1000// expires in one year
    });

    return token;
}
```

More info:
[Authenticate a Node.js API with JSON Web Tokens](https://scotch.io/tutorials/authenticate-a-node-js-api-with-json-web-tokens)
[Securing Node.js RESTful APIs with JSON Web Tokens](https://medium.freecodecamp.org/securing-node-js-restful-apis-with-json-web-tokens-9f811a92bb52)