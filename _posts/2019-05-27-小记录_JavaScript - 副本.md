---
layout: post
title: "С��¼-JavaScript"
subtitle: ''
date:   2019-05-27 09:50:26 +0800
author: "Pcc"
header-img: "img/about-bg.jpg"
header-mask: 0.3
tags:
  - JavaScript
---


# md�﷨
+ [����](https://www.jianshu.com/p/399e5a3c7cc5)


# bind()��delegate()������

+ `bind()` ����Ϊ��ѡ**<font color="#660000">Ԫ��</font>**���һ�������¼�������򣬲��涨�¼�����ʱ���еĺ�����

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
	+ `delegate()` ����Ϊָ����Ԫ�أ�����**��ѡԪ�ص���Ԫ��**�����һ�������¼�������򣬲��涨����Щ�¼�����ʱ���еĺ�����

	+ ʹ�� `delegate()` �������¼�������������ڵ�ǰ��<font color="#660000">δ��</font>��Ԫ�أ������ɽű���������Ԫ�أ���
```


    $("div").delegate("p,span", "click", function () {
        $(this).slideToggle();//ͨ��ʹ�û���Ч��������ʾ������״̬֮���л�
    })
  
	
```



# canvas�޷������ⲿ��������

+   `window.onload=function() {}`
+    �õȼ������������

```CSS
		@font-face {
            font-family: 'mFont';
            src: url('font/HYSHUHUNTIJ.ttf');
        }

        .canvasBox canvas {  
            font-size: 45px;
            font-family: mFont; //Ҫд
        }
		

```


```
  window.onload=function() {  //Ҫд ����Ϊ�õȼ������������
        var canvas = document.getElementById('myCanvas');
        var ctx = canvas.getContext('2d');
        ctx.font = "50px mFont";
        ctx.fillText("�ı�asdf", 0, 60);
    }
```



# ����Ͱ�ʸ���⡪��SVG�ļ���use��ǩxlink:href��ֵ

+ `$(' use')[0].href.baseVal` ��`$("use").attr("xlink:href","#icon-xuanzhong1-copy");`��ȡuse��ǩxlink:href��ֵ,����ֵ

```

<svg class="icon" aria-hidden="true">
  <use xlink:href="#icon-xuanzhong1-copy"></use>
</svg>
   console.log($(' use')[0].href.baseVal);
   $("use").attr("xlink:href","#icon-xuanzhong1-copy");
 
```



# js��ѡ��

+ ԭ���Ȱ�ȫ���ֵܱ�ң��Լ��ڱ���

```

//ѡ����ʽ��

        $('.canvasBox-bottom>div').click(function () {
			//����һ
            // for(let i=0;i<$('.canvasBox-bottom>div').length;i++){
            //     $($('.canvasBox-bottom>div')[i]).find("use")[0].href.baseVal="#icon-xuanzhong1-copy";
            // }
			//������
            $(this).siblings().find("use").attr("xlink:href","#icon-xuanzhong1-copy");//�Ȱ�ȫ���ֵܱ��
			
			
			
            $(this).find('use')[0].href.baseVal='#icon-xuanzhong1';//���Լ�����

        });
		
```



# window.onload��$(document).ready(function(){})������

+ ִ��ʱ���ϵ�����`window.onload` ����ȵ�**ҳ���ڣ�����ͼƬ�ģ�����Ԫ��**���ص�������к����ִ�С��� `$(document).ready(function(){})`��**DOM�ṹ**������Ϻ�ͻ�ִ�С� 

+  `$(document).ready(function(){})` ���Լ�дΪ `$(function(){})` ����д���

+ `$(window).load(function(){})=====window.onload = function(){}...` ֻ��дһ��




# $().attr() �� $().css������

+ `attr()` �� ��ȡ���޸ĵ���Ԫ�ص����ԣ���img��src���Ժ�alt���ԣ�a���ӵ�href���Եȵȡ�(`width='80'` ����Ԫ������)

+ `css()` ����ȡ���޸ĵ�����ʽ��������ԣ�����style��������ԡ�(`style='width:80px'` ����style����)

# number.toPrecision(x)��parseFloat

+ `number.toPrecision(x)` �����ָ�ʽ��Ϊָ���ĳ���:
+ `passeFloat` ������һ����������

```
var num = new Number(13.3714);
var c = num.toPrecision(3);  // ������:13.34
var d = num.toPrecision(10); // ������:13.37140000


//ת�ٷֱ�
var number=0.33;
var percent=parseFloat((number*100).toPrecision(12))+'%';  //�����percent=33%
       



```



# Canvas�첽����ͼƬ

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
			   let img = new Image();//����һ��imgʵ��
			   // if(url==s[1][0]){
			   // img.setAttribute("crossOrigin",'Anonymous');  //�����������
			   // }
			   // img.crossOrigin = "Anonymous";

			   img.src = url;
			   img.onload = function () {//imgһ������onload�¼����ͱ�ʾ�ɹ�
				   resolve(img);//�ɹ��Ļ������õ����img������ִ��then�����ĵ�һ���ص�����
			   };
			   img.onerror = function (e) {//imgһ������onerror�¼����ͱ�ʾʧ��
				   reject(e);//ʧ�ܵĻ������õ�������Ϣ������ִ��then�����ĵڶ����ص�����
			   }
		   })
	   }
	   get(s);
					
```