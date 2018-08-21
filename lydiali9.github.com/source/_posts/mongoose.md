---
title: mongoose
date: 2018-06-20 10:58:39
tags: ['mongoose']
categories: ['mongoose', 'model', 'schema']
---

因为之前有外企两年的工作经验， 而且身边的同事都是外国人， 我从小学二年级学习英语到毕业，突然发现自己学的是哑巴英语，
每次开会，讨论需求和我的美国老大交流时，心里都特别害怕，也听不明白在说什么， 至于尴尬的场面那也是经常出现的， 所以接下来
就有了英孚英语学习的经历；所以接下来我的大部分Blog将要用英语去写了， 希望也是对自己的一个提升； 朋友们要帮我多多监督啊！

### What's Mongoose?
Mongoose is a MongoDB object modeling tool designed to work in an asynchronous environment.

Defining a model is as easy as:
```javascript
'use strict';

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

### Installation

```javascript
npm install mongoose
```

### Connecting to MongoDB

#### config（default.js）
```javascript
'use strict';

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

#### mongodb(db.js)

```javascript
'use strict';

/*import mongoose from 'mongoose';
import config from 'config-lite';
import chalk from 'chalk';*/

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

### How to start mongodb
```javascript
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

app.use(router);

app.use(history());
app.listen(config.port, () => {
	console.log(
		chalk.green(`成功监听端口：${config.port}`)
	)
});
```

### Available Schema Types
* String
* Number
* Date
* Boolean
* Buffer (v2.x only)
* ObjectId(Schema.ObjectId)
* Mixed(Schema.Types.Mixed)
* Array


### Additional options
* default: {Function|value}
* required: {Boolean}
* select: {Boolean}
* get: {Function}
* set: {Function}
* index: {Boolean|Object}
* unique: {Boolean}
* sparse: {Boolean}
* validate: {Function|RegExp|Array}

### CRUD

#### C
> Model.create(doc(s), [callback])

```javascript
// save the data of vehicle
VehicleModel.create(params, function(error, docs){
    if(err) {
        $logger.error('method: add_vehicle, msg: 添加车辆失败');
        res.json({code: $code.FALSE, errmsg: "添加车辆失败", data: ""});
    } else {
        res.json({code: $code.SUCCESS, msg: '添加车辆成功', content: docs});
    }
});
```

> Model#save([options], [options.safe], [options.validateBeforeSave], [fn])
> Model.insertMany(doc(s), [options], [callback])

#### R
> Model.find(conditions, [projection], [options], [callback])

conditions： conditions；projection：the field of response；options：options；callback：methed.

```javascript
VehicleModel.find({ uid: body.uid }, function (err, results) {
if(err) {
    $logger.error('method: query_vehicle, msg: 该用户尚未添加车辆');
    res.json({code: $code.NO_EXIST, msg: '该用户尚未添加车辆', content: {}});
} else {
    let content = [];
}
```

> Model.findOne([conditions], [projection], [options], [callback])
> Model.findById(id, [projection], [options], [callback])

#### U
> Model.update(conditions, doc, [options], [callback])

```javascript
let params = {
    vehicle_type: body.vehicle_type,
    plate_no: body.plate_no,
    VIN: body.VIN,
    engine_no: body.engine_no
}
VehicleModel.findById(body._id, function (err, result){
    if(result) {
        // update the data of vehicle
        VehicleModel.update({_id: body._id, uid: body.uid}, params, function(error, docs){
            if(err) {
                $logger.error('method: update_vehicle, msg: 修改车辆失败' + err);
                res.json({code: $code.FALSE, errmsg: "修改车辆失败", data: ""});
            } else {
                res.json({code: $code.SUCCESS, msg: '修改车辆成功', content: docs});
            }
        });
    } else {
        $logger.warn('method: update_vehicle, msg: id为' + body._id + '的车辆不存在');
        res.json({code: $code.NO_EXIST, errmsg: "此车辆不存在或已被删除", data: ""});
    }
});
```

> Model.updateMany(conditions, doc, [options], [callback])
> Model.updateOne(conditions, doc, [options], [callback])
> Model.findByIdAndUpdate(id, [update], [options], [callback])
> Model.findOneAndUpdate([conditions], [update], [options], [callback])


#### D
> Model.remove(conditions, [callback])

```javascript
VehicleModel.remove({_id: body._id, uid: body.uid}, function(error, docs){
    if(err) {
        $logger.error('method: delete_vehicle, msg: 删除车辆失败' + err);
        res.json({code: $code.FALSE, errmsg: "删除车辆失败", data: ""});
    } else {
        res.json({code: $code.SUCCESS, msg: '删除车辆成功', content: docs});
    }
});
```

> Model.findByIdAndRemove(id, [options], [callback])
> Model.findOneAndRemove(conditions, [options], [callback])

More info:
* [mongoosejs](http://mongoosejs.com/docs/index.html#)
* [mongoose](http://mongoosejs.com/docs/2.7.x/docs/finding-documents.html)
* [mongodb](https://docs.mongodb.com/manual/introduction/)



