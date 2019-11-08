---
layout: post
title: "Vue"
subtitle: ''
date:   2019-06-04 17:00:26 +0800
author: "Pcc"
header-img: "img/pcc_vue_lv.jpg"
header-mask: 0.5
tags:
  - Vue
---


[TOC]
# 基于Vue的Ui框架

- 饿了么公司基于vue开的的vue的Ui组件库
  - Element Ui      基于vue  pc端的UI框架  
  - MintUi             基于vue 移动端的ui框架	

[官网主页](https://element.eleme.cn/#/zh-CN)

> wabpack是模块化打包工具，打包所有的脚本



## mintUI的使用：

1.找官网

2.安装   npm install mint-ui -S         -S表示  --save

3.引入mint Ui的css 和 插件

```javascript
	import Mint from 'mint-ui';

	Vue.use(Mint);
```

```javascript
	import 'mint-ui/lib/style.css'
```

4.看文档直接使用。

在mintUi组件上面执行事件的写法

Github上找mintUi下载项目

```html
@click.native

<mt-button @click.native="sheetVisible = true" size="large">点击上拉 action sheet</mt-button>
```



## element UI的使用：

1.找官网  http://element.eleme.io/#/zh-CN/component/quickstart

2.安装  cnpm i element-ui -S         -S表示  --save

3.引入element UI的css 和 插件

```javascript
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';
Vue.use(ElementUI);
```

4、`webpack.config.js  `配置file_loader     （报错.ttf）
   http://element.eleme.io/1.4/#/zh-CN/component/quickstart

```javascript
	  {
		test: /\.(eot|svg|ttf|woff|woff2)(\?\S*)?$/,
		loader: 'file-loader'
       }
```

5.看文档直接使用。

### element UI组件的单独使用（第一种方法）

1、`cnpm install babel-plugin-component -D  `

2、找到.babelrc 配置文件

把下面的

```javascript
	{
	  "presets": [
	    ["env", { "modules": false }],
	    "stage-3"
	  ]
	}
```

改为  注意：	

```javascript
	{
	  "presets": [["env", { "modules": false }]],
	  "plugins": [
	    [
	      "component",
	      {
		"libraryName": "element-ui",
		"styleLibraryName": "theme-chalk"
	      }
	    ]
	  ]
	}


```

3、

```javascript
import { Button, Select } from 'element-ui';

Vue.use(Button)
Vue.use(Select)
```





### element UI组件的单独使用（第二种方法）：



```javascript
import { Button, Select } from 'element-ui';

Vue.use(Button)
Vue.use(Select)
```

```javascript
引入对应的css

	import 'element-ui/lib/theme-chalk/index.css';

如果报错： webpack.config.js  配置file_loader

	  {
		test: /\.(eot|svg|ttf|woff|woff2)(\?\S*)?$/,
		loader: 'file-loader'
          }
```

​	