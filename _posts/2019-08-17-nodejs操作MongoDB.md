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

## node操作**Mongoose**

Mongoose是一个将JavaScript对象与数据库产生关系的一个框架，object related model。操作对象，就是操作数据库了；对象产生了，同时也持久化了。

[官网](http://mongoosejs.com/)

示例：

```javascript
//引包，并不需要引用mongodb这个包
var mongoose = require('mongoose');
//链接数据库,haha是数据库名字
mongoose.connect('mongodb://localhost/haha');

//创建了一个模型。猫的模型。所有的猫，都有名字，是字符串。“类”。
var Cat = mongoose.model('Cat', { name: String });
//实例化一只猫
var kitty = new Cat({ name: 'Zildjian' });
//调用这只猫的save方法，保存这只猫
kitty.save(function (err) {
  console.log('喵喵喵');
});

var tom = new Cat({"name":"汤姆"});
tom.save(function(){
	console.log('喵喵喵');
});

```

上面的代码中，没有一个语句是明显的操作数据库，感觉都在创建类、实例化类、调用类的方法。都在操作对象，但是数据库同步被持久了。

mongoose的哲学，就是让你用操作对象的方式操作数据库。

- 创建一个模型
  `mongoose.model("Cat",{"name" : String , "age" : Integer}); `
- 就可以被实例化
  `var kitty = new Cat({ name: 'Zildjian' });`
  然后这个实例就可以被save。
- mongoose首先要想明白一件事儿，所有的操作都不是对数据库进行的。所有的操作都是对类、实例进行的。但是数据库的持久化自动完成了。

### **1、数据库连接**

公式：

```javascript
var mongoose = require('mongoose');
//创建数据库连接
var db      = mongoose.createConnection('mongodb://127.0.0.1:27017/haha');
//监听open事件
db.once('open', function (callback) {
    console.log("数据库成功连接");
});
```

### **2、定义模型**

**创造schema → 定义一些schema的静态方法 → 创造模型**

 SchemaTypes are:

- [String](https://mongoosejs.com/docs/schematypes.html#strings)
- [Number](https://mongoosejs.com/docs/schematypes.html#numbers)
- [Date](https://mongoosejs.com/docs/schematypes.html#dates)
- [Buffer](https://mongoosejs.com/docs/schematypes.html#buffers)
- [Boolean](https://mongoosejs.com/docs/schematypes.html#booleans)
- [Mixed](https://mongoosejs.com/docs/schematypes.html#mixed)
- [ObjectId](https://mongoosejs.com/docs/schematypes.html#objectids)
- [Array](https://mongoosejs.com/docs/schematypes.html#arrays)
- [Decimal128](https://mongoosejs.com/docs/api.html#mongoose_Mongoose-Decimal128)
- [Map](https://mongoosejs.com/docs/schematypes.html#maps)

```javascript
//创建了一个schema结构。
var studentSchema = new mongoose.Schema({
    name     :  {type : String},
    age      :  {type : Number},
    sex      :  {type : String}
});
//创建静态方法
studentSchema.statics.zhaoren = function(name, callback) {
    this.model('Student').find({name: name}, callback);   //this.model('Student')指的是当前这个类
};
//创建修改的静态方法
studentSchema.statics.xiugai = function(conditions,update,options,callback){
    this.model("Student").update(conditions, update, options, callback);
}
//创建了一个模型，就是学生模型，就是学生类。
//类是基于schema创建的。
var studentModel = db.model('Student', studentSchema);
```

 

解释一下，什么是静态方法，什么是类方法：

```javascript
//类
function Student(){
	
}

//实例化一个学生
var xiaoming = new Student();
//实例方法，因为这个sleep方法的执行者是类的实例
xiaoming.sleep();


//静态方法（类方法），这个方法的执行者是这个类，不是这个类的实例。
Student.findAllBuJiGe();
```

前台界面：不操作数据库，只操作类！

### 3、虚拟属性：

```javascript
// define a schema
var personSchema = new Schema({
  name: {
    first: String,
    last: String
  }
});
//实例方法  studentSchema.methods.方法名 = function(){}；
personSchema.
// compile our model
var Person = mongoose.model('Person', personSchema);

// create a document
var bad = new Person({
    name: { first: 'Walter', last: 'White' }
});
```



### Demo



### 步骤

一、db链接mongoose

二、创建了一个schema结构：`new mongoose.Schema({});`

三、定义实例方法：`studentSchema.methods.方法名 = function(){}；`

四、创建模型：`mongoose.model("Kecheng",kechengSchema);`

```javascript

var mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/xuanke');

var db = mongoose.connection;
db.once('open', function (callback) {
    console.log("数据库成功打开");
});

//学生
var studentSchema = new mongoose.Schema({
    "name" : String,
    "age" : Number,
    "sex" : String
});
//实例方法，涨一岁
studentSchema.methods.zhangyisui = function(){
    this.age++;
    this.save();
}

var Student = mongoose.model("Student",studentSchema);

//课程。
var kechengSchema = new mongoose.Schema({
    "name" : String,
    "info" : String,
    "student" : [studentSchema]
});
//添加学生
kechengSchema.methods.tianjiaxuesheng = function(studentObj,callback){
    this.student.push(studentObj);
    this.save(function(){
        callback();
    });
}

kechengSchema.methods.zhaoxuesheng = function(num,callback){

    Student.findOne({"name":this.student[num].name},function(err,result){
        callback(err,result);
    });
}

var Kecheng = mongoose.model("Kecheng",kechengSchema);

//实例化几个学生
//var xiaoming = new Student({"name":"小明","age":12,"sex":"男"});
//xiaoming.save();

//var shuxue = new Kecheng({
//    "name" : "数学课",
//    "info" : "学数学的"
//});
//////
//shuxue.tianjiaxuesheng(xiaoming,function(){
//    console.log("添加成功");
//});

////寻找学生小明
//Student.findOne({"name":"小明"},function(err,student){
//    student.zhangyisui();
//});
//
//////通过课程找学生
Kecheng.findOne({"name":"数学课"},function(err,kecheng){
    kecheng.zhaoxuesheng(0,function(err,result){
        console.log(result);
    });
});


```







