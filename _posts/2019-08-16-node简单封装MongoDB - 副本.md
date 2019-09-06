---
layout: post
title: "node操作MongoDB"
subtitle: ''
date:   2019-08-16 11:00:26 +0800
author: "Pcc"
header-img: "img/pcc_time.jpg"
header-mask: 0.3
tags:

  - node操作MongoDB

---

## node操作MongoDB（简单的封装DAO）

+ `npm install mongodb --save`(最新版本)
+ [官网手册](https://docs.mongodb.com/manual/)

### db.js文件

```js
var MongoClient = require('mongodb').MongoClient;
const assert = require('assert');

const url = 'mongodb://localhost:27017';
const dbName = 'myproject';


//连接函数
function _connectionDB(callback) {

    const client = new MongoClient(url, { useNewUrlParser: true });

    client.connect(function (err) {
        if (err) {
            console.log("连接失败");
            callback(err, null);
            return;
        }
        console.log("mongoDB连接成功！");
        const db = client.db(dbName);
        callback(err, db);
    });
};

//插入json
exports.insertOne = function (collectionName, json, callback) {
    _connectionDB(function (err, db) {

        const collection = db.collection(collectionName);
        //json:[{a : 1}, {a : 2}, {a : 3}]

        //插入json数据
        collection.insertMany(json, function (err, result) {

            //错误提示
            // assert.equal(err, null);
            if (err) {
                console.log("插入失败");
                callback(err, result);
                return;
            }

            console.log("成功插入JSON");
            callback(null, result);
            // db.close(); //关闭数据库
        });

    });

}

//查看、查找
exports.find = function (collectionName, json,C, F) {
    if (arguments.length == 3) {
        var callback = C;
        var skipnumber = 0;
        //数目限制
        var limit = 0;
    }else  if (arguments.length == 4) {
        var callback = F;
        var args = C;
        //应该省略的条数
        var skipnumber = args.pageamount * args.page || 0;
        //数目限制
        var limit = args.pageamount || 0;
        //排序方式
        var sort = args.sort || {};
    }else{
        throw new Error("find函数的参数个数，必须是3个，或者4个。");
        return;
    }


    _connectionDB(function (err, db) {  //获取文档集合
        const collection = db.collection(collectionName);
        collection.find(json).skip(skipnumber).limit(limit).sort(sort).toArray(function (err, docs) {
            if (err) {
                console.log("查看失败");
                callback(err, docs);
                return;
            }
            console.log("Found the following records");
            console.log(docs);
            callback(null, docs);
         
        });
    })
}
//修改一个
exports.updateOne = function (collectionName, json1, json2, callback) {
    _connectionDB(function (err, db) {  //获取文档集合
        const collection = db.collection(collectionName);
        // json1：{ a : 2 }，json2:{ $set: { b : 1 } }
        collection.updateOne(json1, json2, function (err, result) {
            console.log("已更新一个");
            callback(err, result);
            db.close(); //关闭数据库
        });
    })
}

//修改
exports.updateMany = function (collectionName, json1, json2, callback) {
    _connectionDB(function (err, db) {
        const collection = db.collection(collectionName);
        // json1：{ a : 2 }，json2:{ $set: { b : 1 } }
        collection.updateMany(json1, json2, function (err, result) {
            console.log("已更新全部");
            callback(err, result);
            db.close(); //关闭数据库
        });
    })
}


//删除指定
exports.removeOne = function (collectionName, json, callback) {
    _connectionDB(function (err, db) {
        const collection = db.collection(collectionName);

        collection.deleteOne(json, function (err, result) {
            if (err) {
                console.log("删除失败");
                callback(err, result);
                db.close(); //关闭数据库
                return;
            }
            console.log("删除全部");
            callback(null, result);
            db.close(); //关闭数据库
        });

    })
}



```

 

app.js部分

```js

var express = require("express");
var app = express();
var db = require("./model/db.js");

//插入数据，使用我们自己封装db模块，就是DAO。
app.get("/charu", function (req, res) {

    //http://127.0.0.1:3000/charu?name=xiaoming&age=10
    var name = req.query.name;  //express中读取get参数很简单
    var age = parseInt(req.query.age);

    console.log(req.query);//{ name: 'xiaoming', age: '10' }

    var connectionName = "add";
    var insertJson = [{ "name": name, "age": age, "hoddy": "chifan" }];

    db.insertOne(connectionName, insertJson, function (err, result) {
        if (err) {
            console.log("插入失败");
            return;
        }
        res.send(result);
    });
});
app.get("/look", function (req, res) {
    var connectionName = "add";
    var json = { "age": 4 };     //看什么
    var page = req.query.page;  //express中读取get参数很简单
    
    //分页查询
    if (page) {
        var json2 = { "pageamount": 3, "page": page };
        db.find(connectionName, json, json2,
            function (err, docs) {
                if (err) {
                    console.log("分页查看失败");
                    return;
                }
                res.send(docs);
            }
        );
    //条件全部查询
    } else{

        db.find(connectionName, json,
            function (err, docs) {
                if (err) {
                    console.log("查看失败");
                    return;
                }
                res.send(docs);
            }
        );
    }



})
app.get("/xiugai", function (req, res) {
    var connectionName = "teacher";
    var json1 = { "name": "小红" };     //改什么
    var json2 = { $set: { name: "北lal", "age": 18 } };     //怎么改

    db.updateMany(connectionName, json1, json2,
        function (err, result) {   //改完之后做什么
            if (err) {
                console.log("修改失败");
                return;
            }
            res.send(result);
        }
    );

})
app.get("/shan", function (req, res) {
    var connectionName = "teacher";
    var json = { "name": "北lal" };     //删什么

    db.removeOne(connectionName, json,
        function (err, result) {
            if (err) {
                console.log("删除失败");
                return;
            }
            res.send(result);
        }
    );


})


app.listen(3000);
```

其中的分页查找：

+ `find().skip(skipnumber)`  略过几条
+ `limit(limit)`一页几条

![分页查找](https://raw.githubusercontent.com/Panssorcc/picee/master/images/node-mongoDB-%E5%88%86%E9%A1%B5%E6%9F%A5%E8%AF%A2.png)