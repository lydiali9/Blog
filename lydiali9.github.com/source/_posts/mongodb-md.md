title: MongoDB
date: 2016-02-01 08:51:34
tags: ['MongoDB', 'inset', 'save', 'update', 'remove', '$set', 'drop', '$push', '$inc', '$unset']
categories: ['MongoDB']
---

## MongoDB相关的基本操作

### 在集合中插入文档
MongoDB 使用 insert() 或 save() 方法向集合中插入文档

#### 插入单个文档

```
$ db.COLLECTION_NAME.insert({"type": "CD", "article": "MongoDB"})
```

#### 批量插入文档

```
$ for (var i = 0; i <= 9; i++) {
    db.COLLECTION_NAME.insert({"type": "CD", "article": "MongoDB", "order": i})
  }
```

插入文档你也可以使用 db.COLLECTION_NAME.save(document) 命令; 如果不指定 _id 字段 save() 方法类似于 insert() 方法。如果指定 _id 字段，则会更新该 _id 的数据

### 删除数据

#### 删除单个文档

```
$ db.COLLECTION_NAME.remove(
   <query>,
   <justOne>
)
```

参数说明：
* query: (可选)删除文档的条件
* justOne: (可选）如果设为 true 或 1，则只删除一个文档

#### 删除整个集合

```
$ db.COLLECTION_NAME.drop()
```

比较： remove集合的本身和索引不会被删除，而drop效率更高，相应的索引也会被删除

### 更新文档

```
$ db.COLLECTION_NAME.update(
   <query>,
   <update>,
   {
     upsert: <boolean>,
     multi: <boolean>
   }
)
```

参数说明：
* query：update的查询条件
* update：跟新的对象，这里可以使用一些操作符，如$set等
* upsert：（可选）如果不存在update的记录，是否插入新的文档,true为插入，默认是false，不插入
* multi：（可选）默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新

```
$ db.COLLECTION_NAME.update({"title": "MongoDB"},{$set:{"title": "MongoDB"}}, {upsert： true, multi: true})
```

以上是修改多条相同的文档，不存在就进行插入；当然也可以使用save，但是要指定文档的 _id字段，否则也是插入

#### 使用修改器进行局部更新

##### 1. $set设置字段值

```
$ db.COLLECTION_NAME.update({"title": "MongoDB"}， {$set: {"name": "title"}})
```

将某个字段设定为指定值

##### 2. $unset删除指定字段

```
$ db.COLLECTION_NAME.update({"title": "MongoDB"}， {$unset: {"name": 1}})
```

可以删除指定的字段值，从文档中删除name键和name的值

##### 3. $inc增加值

```
$ db.COLLECTION_NAME.update({"title": "MongoDB"}， {$inc: {"read": 91}})
```

可以为指定的键执行跟新操作,如果字段存在，就将read字段（数值类型）加91，否则就创建该字段

##### 4. $push在指定字段中添加某个值

```
$ db.COLLECTION_NAME.update({"title": "MongoDB"}， {$push: {"author": "lydiali, lydiali91"}})
```

可以在指定字段中添加某个值。如果该字段是个数组，那么该值将被添加到数组中；如果该字段不存在，那么该字段的值将被设置为数组；如果该字段存在，但不是数组，那么将中断操作

##### 5. 指定数组中的多个值
如果给指定的数组中添加几个不同的值，可以使用可选的操作符$each

```
$ db.COLLECTION_NAME.update({"title": "MongoDB"}， {$push: {"author": $each: {["lydiali91", "lydiali"]}}})
```

在使用$$each时, 当然也可以使用$slice, 这样可以限制$push操作符中数组内元素的数量. $slice接受负数或零，负数表示将保证数组中的最后n个元素会被保留，而零表示清空数组

```
$ db.COLLECTION_NAME.update({"title": "MongoDB"}， {$push: {"author": $each: {["lydiali91", "lydiali"]， $slice: -2}}})
```

以上操作，操作符$slice保证不仅两个新值会被添加到数组中，还保证将把数组的大小限定为指定值
* 注意：操作符$$slice必须是$push操作中的第一个修改操作符

##### 6. $addToSet向数组中添加数据
它是另外一个可以向数组中添加数组的操作符, 与$$push相比, 不同之处就是, 它只有在数据不存在的时候, 该操作符才能将数据添加到数组中, $addToSet默认就接受一个值; 不过, 也可以结合$each操作符指定额外的参数

```
$ db.COLLECTION_NAME.update({"title": "MongoDB"}， {$addToSet: {"author": $each: {["lydiali91", "lydiali"]}}})
```

##### 7. 从数组中删除元素
$pop从数组中删除单个元素; 该元素只允许删除数组中的第一个或最后一个，使用正数和零就会删除数组中最后一个元素， 使用负数就会删除数组中的第一个元素

```
$ db.COLLECTION_NAME.update({"title": "MongoDB"}， {$pop: {"author": 1}}})
```

##### 8. 删除所有指定的值
$$pull操作符可以从数组中删除所有值，如果数组中有多个元素的值相同，使用$pull非常有用

```
$ db.COLLECTION_NAME.update({"title": "MongoDB"}， {$pull: {"author": "lydiali91, lydiali"}}})
```

##### 9. 删除数组中的多个元素
$pullAll还可以从数组中删除多个含有不同值的元素

```
$ db.COLLECTION_NAME.update({"title": "MongoDB"}， {$pullAll: {"author": ["lydiali91, lydiali"]}}})
```

### 相关链接
* [MongoDB 英文文档](https://docs.mongodb.org/manual/)
* [MongoDB 教程](http://www.runoob.com/mongodb/mongodb-tutorial.html)
