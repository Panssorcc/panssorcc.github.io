---

layout: post
title: "nodejs操作MongoDB"
subtitle: ''
date:   2019-08-16 16:00:26 +0800
author: "Pcc"
header-img: "img/pcc_time.jpg"
header-mask: 0.3
tags:

  - MongoDB

---

## nodejs操作MongoDB（简单的封装DAO）

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





### Demo

+ app.js部分

```JS

var express = require("express");
var app = express();
var db = require("./model/db.js");
var formidable = require('formidable');
var ObjectId = require('mongodb').ObjectID;


//设置模板引擎
app.set("view engine", "ejs");

//静态
app.use(express.static("./public"));
//显示留言列表
app.get("/", function (req, res, next) {
    db.getAllCount("liuyanben",function(err,count){
        console.log(Math.ceil(count /5));
       
        res.render("index",{
            "pageamount" : Math.ceil(count / 5)//几页
        });
    });
});

//读取所有留言，这个页面是供Ajax使用的
app.get("/du", function (req, res, next) {
    //可以接受一个参数
    var page = parseInt(req.query.page);

    db.find("liuyanben",{},{"sort":{"shijian":-1},"pageamount":5,"page":page},function(err,result){
       
        res.json({"result":result});
    });
});

//处理留言
app.post("/tijiao", function (req, res, next) {
    var form = new formidable.IncomingForm();

    form.parse(req, function (err, fields) {
      
        //写入数据库
        db.insertOne("liuyanben", [{
            "xingming" : fields.xingming,
            "liuyan" : fields.liuyan,
            "shijian" : new Date()
        }], function (err, result) {
            if(err){
                res.send({"result":-1}); //-1是给Ajax看的
                return;
            }
            res.json({"result":1});
        });
    });
});
//删除
app.get("/shanchu",function(req,res,next){
    //得到参数
    var id = req.query.id;
    
    db.removeOne("liuyanben",{"_id":ObjectId(id)},function(err,result){

        res.redirect("/");
    });
})

app.listen(3000);
```

+ index.ejs部分

```javascript

<script type="text/javascript">
    var nowpage = 1;

    //给第一个页面，补一个active
    $(".yemaanniu:first").addClass("active");

    //给所有的页码按钮，添加监听
    $(".yemaanniu").click(function(){

        nowpage =  parseInt($(this).attr("data-page"));
        //重新发起请求，即可
        getData(nowpage);

        $(this).addClass("active").siblings().removeClass("active");
    });

    //默认请求第一页数据
    getData(1);

    //Ajax请求数据
    function getData(page) {
        //真实page是从0开始算的
        $.get("/du?page=" + (page - 1), function (result) {
            //这里接收是result，但是这个json里面有一个key叫做result。
            //得到模板，弄成模板函数
            var compiled = _.template($("#moban").html());
            //清空全部留言中的所有节点
            $("#quanbuliuyan").html("");
            console.log(result);
            for (var i = 0; i < result.result.length; i++) {
                //数据绑定
                var html = compiled({
                    liuyan: result.result[i].liuyan,
                    xingming: result.result[i].xingming,
                    shijian: result.result[i].shijian,
                    id: result.result[i]._id
                });
                //DOM操作，添加节点
                $("#quanbuliuyan").append($(html));
            }
        });
    }

    //Ajax提交表单
    $("#tijiao").click(function () {
        $("#shibai").hide();
        $("#chenggong").hide();
        $.post("/tijiao", {
            "xingming": $("#xingming").val(),
            "liuyan": $("#liuyan").val()
        }, function (result) {
            if (result.result == -1) {
                $("#shibai").fadeIn();
            } else if (result.result == 1) {
                //提交成功
                $("#chenggong").fadeIn();
                //数据库真的存储了，但是当前页面无法显示。这是因为需要刷新
                //才能用ajax从/du中得到新的。所以我们先用一个假盒子凑出来。
                var compiled = _.template($("#moban").html());
                var html = compiled({liuyan: $("#liuyan").val(), xingming: $("#xingming").val(), shijian: new Date()});
                $(html).insertBefore($("#quanbuliuyan"));
            }
        });
    });
</script>

```

