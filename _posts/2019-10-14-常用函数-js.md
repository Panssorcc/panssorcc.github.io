---
layout: post
title: "小记录-JavaScript"
subtitle: ''
date:   2019-05-27 09:50:26 +0800
author: "Pcc"
header-img: "img/post-bg-js-version.jpg"
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



# querySelector() 和 document.getElementsByTagName 的区别

+ querySelector() 方法  <br/>仅仅返回匹配指定选择器的第一个元素。如果你需要返回所有的元素，请使用 querySelectorAll() 方法替代。
	+  querySelector() 方法获取li标签 得到 ==>    `<li>第一个标签</li>`
	+  querySelectorAll() 方法获取li标签 得到 ==>   `NodeList(5) [li, li, li, li, li]`

+ document.getElementsByTagName()方法  <br/>返回带有指定标签名的对象的集合。
	+ 获取li标签 得到 ==> `HTMLCollection(5)?[li, li, li, li, li]`

#### document.getAttribute(属性) 和 setAttribute(属性，属性值)的区别

#### document.getElementByIdx_x( ) 获取指定名称或标签所对应的ID

#### document.getElementsByName( ) 获取指定名称或标签所对应的name




# $().attr() 和 $().css的区别

+ `attr()` ：<br/> 获取和修改的是元素的属性，如img的src属性和alt属性，a链接的href属性等等。(`width='80'` 属于元素属性)

+ `css()` ：<br/>获取和修改的是样式里面的属性，即是style里面的属性。(`style='width:80px'` 属于style里面)

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

### 什么是arguments?

它是**JS的一个内置对象**，常被人们所忽略，但实际上确很重要，JS不像JAVA是显示传递参数，JS传的是形参，可以传也可以不传，若方法里没有写参数却传入了参数，该如何拿到参数呢，答案就是arguments。

+ #### 该如何拿到参数？

  每一个函数都有一个arguments对象，它包括了函数所要调的参数，通常我们把它当作数组使用，用它的length得到参数数量，但它却不是数组
  
+ #### 把arguments转换成一个真正的数组

  有时我们希望将它转换成真正的数组使用，可以使用下面的代码：


```js
function howManyArgs() {
  alert(arguments.length);//2
    var args = Array.prototype.slice.call(arguments);
    alert(args);// string,HHHAA
}
howManyArgs("string", 45);
```

### 网页渲染

浏览器拿到HTML代码之后，它是如何显示给大家看的呢？其实所谓的渲染就是讲HTML代码根据CSS定义的规则显示在浏览器窗口中的这个过程

1. 首先浏览器先把这个HTML文档先转化为DOM树，然后根据这个DOM树，进行渲染，转化为可视的东西
2. 在渲染的时候，浏览器从上到下依次渲染DOM树，当发现有外链资源或脚本`<link>、<img>、<script>`的时候会异步发起请求加载，同时DOM树的解析继续。一般我们都会把style都放在head里面，那么这样浏览器就先拿到了css样式文件，他就知道如何讲每个元素绘出来放在哪个位置。
3. 如果遇到图片`<img>`浏览器不会等图片加载完再去加载，而是继续加载，这样就会出现个问题，当图片加载完时，在页面插入图片会影响页面的结果，浏览器就又要重新排布，这样浏览器又要花费时间跟经历去排布，*所有如果图片是固定的尺寸，我们在写CSS的时候就应该给他加上宽高，浏览器就会预留一个位置给图片，这样就避免了重新排布*

- ##### 回流（重排reflow）

  上文中将到的重新排布就是回流，网页加载慢，有一部分原因就是回流，因为浏览器要耗更多的时间去重新排布渲染，但回流也是不可避免的，因为网页中的一些效果，如鼠标滑过、点击效果等引起了页面上某些元素的占位面积、定位方式、边距包括浏览器的伸缩等的变化都会发生回流。但也有些事可以避免的，例如上文中说的给图片定死宽高。

- ##### 重绘（repaint）

  有个和 reflow 看上去差不多的术语：repaint，中文叫重绘。如果只是改变某个元素的背景色、文字颜色、边框颜色等等不影响它周围或内部布局的属性，将只会引起浏览器 repaint。repaint 的速度明显快于reflow

  ```javascript
  $(img).css('width',200px)//重排
  $(body).css('backgroud','#fff')//重绘
  ```

