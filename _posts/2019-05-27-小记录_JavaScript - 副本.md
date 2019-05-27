---
layout: post
title: "小记录-JavaScript"
subtitle: ''
date:   2019-05-27 09:50:26 +0800
author: "Pcc"
header-img: "img/about-bg.jpg"
header-mask: 0.3
tags:
  - JavaScript
---


# md语法
+ [链接](https://www.jianshu.com/p/399e5a3c7cc5)


# bind()和delegate()的区别

+ `bind()` 方法为被选**<font color="#660000">元素</font>**添加一个或多个事件处理程序，并规定事件发生时运行的函数。

```

$(document).ready(function(){
  $("button").bind({
    click:function(){$("p").slideToggle();},
    mouseover:function(){$("body").css("background-color","red");},  
    mouseout:function(){$("body").css("background-color","#FFFFFF");}  
  });
});

```

+  delegate()
	+ `delegate()` 方法为指定的元素（属于**被选元素的子元素**）添加一个或多个事件处理程序，并规定当这些事件发生时运行的函数。

	+ 使用 `delegate()` 方法的事件处理程序适用于当前或<font color="#660000">未来</font>的元素（比如由脚本创建的新元素）。
```


    $("div").delegate("p,span", "click", function () {
        $(this).slideToggle();//通过使用滑动效果，在显示和隐藏状态之间切换
    })
  
	
```



# canvas无法引入外部字体问题

+   `window.onload=function() {}`
+    得等加载完才能引入

```CSS
		@font-face {
            font-family: 'mFont';
            src: url('font/HYSHUHUNTIJ.ttf');
        }

        .canvasBox canvas {  
            font-size: 45px;
            font-family: mFont; //要写
        }
		

```


```
  window.onload=function() {  //要写 ，因为得等加载完才能引入
        var canvas = document.getElementById('myCanvas');
        var ctx = canvas.getContext('2d');
        ctx.font = "50px mFont";
        ctx.fillText("文本asdf", 0, 60);
    }
```



# 阿里巴巴矢量库——SVG文件中use标签xlink:href的值

+ `$(' use')[0].href.baseVal` 或`$("use").attr("xlink:href","#icon-xuanzhong1-copy");`获取use标签xlink:href的值,设置值

```

<svg class="icon" aria-hidden="true">
  <use xlink:href="#icon-xuanzhong1-copy"></use>
</svg>
   console.log($(' use')[0].href.baseVal);
   $("use").attr("xlink:href","#icon-xuanzhong1-copy");
 
```



# js单选框

+ 原理：先把全部兄弟变灰，自己在变亮

```

//选择样式框

        $('.canvasBox-bottom>div').click(function () {
			//方法一
            // for(let i=0;i<$('.canvasBox-bottom>div').length;i++){
            //     $($('.canvasBox-bottom>div')[i]).find("use")[0].href.baseVal="#icon-xuanzhong1-copy";
            // }
			//方法二
            $(this).siblings().find("use").attr("xlink:href","#icon-xuanzhong1-copy");//先把全部兄弟变灰
			
			
			
            $(this).find('use')[0].href.baseVal='#icon-xuanzhong1';//在自己变亮

        });
		
```



# window.onload和$(document).ready(function(){})的区别

+ 执行时间上的区别：`window.onload` 必须等到**页面内（包括图片的）所有元素**加载到浏览器中后才能执行。而 `$(document).ready(function(){})`是**DOM结构**加载完毕后就会执行。 

+  `$(document).ready(function(){})` 可以简写为 `$(function(){})` 。可写多个

+ `$(window).load(function(){})=====window.onload = function(){}...` 只能写一个




# $().attr() 和 $().css的区别

+ `attr()` ： 获取和修改的是元素的属性，如img的src属性和alt属性，a链接的href属性等等。(`width='80'` 属于元素属性)

+ `css()` ：获取和修改的是样式里面的属性，即是style里面的属性。(`style='width:80px'` 属于style里面)

# number.toPrecision(x)和parseFloat

+ `number.toPrecision(x)` 把数字格式化为指定的长度:
+ `passeFloat` 并返回一个浮点数。

```
var num = new Number(13.3714);
var c = num.toPrecision(3);  // 输出结果:13.34
var d = num.toPrecision(10); // 输出结果:13.37140000


//转百分比
var number=0.33;
var percent=parseFloat((number*100).toPrecision(12))+'%';  //结果：percent=33%
       



```



# Canvas异步加载图片

```
	   var c = document.getElementById("cav");
	   var cxt = c.getContext("2d");


	   var s=[["http://img5.imgtn.bdimg.com/it/u=1200889471,2010793696&fm=26&gp=0.jpg",0,0,400,150],
		   ["https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=2608216835,979136783&fm=26&gp=0.jpg",5,4,100,100]];

	   async function get(s){
		   for(let i=0;i<s.length;i++){
			   console.log(s,i);
			   let p=await loadImg(s[i][0]);
			   cxt.drawImage(p,s[i][1],s[i][2],s[i][3],s[i][4]);
		   }

	   }
	   function loadImg(url) {
		   return new Promise((resolve,reject)=>{
			   let img = new Image();//创建一个img实例
			   // if(url==s[1][0]){
			   // img.setAttribute("crossOrigin",'Anonymous');  //处理跨域问题
			   // }
			   // img.crossOrigin = "Anonymous";

			   img.src = url;
			   img.onload = function () {//img一旦触发onload事件，就表示成功
				   resolve(img);//成功的话，就拿到这个img，并且执行then方法的第一个回调函数
			   };
			   img.onerror = function (e) {//img一旦触发onerror事件，就表示失败
				   reject(e);//失败的话，就拿到错误信息，并且执行then方法的第二个回调函数
			   }
		   })
	   }
	   get(s);
					
```