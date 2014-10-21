---
layout: post
title: "Sencha Touch问题汇总"
date: 2014-10-12 09:42:19 +0800
comments: true
categories: Sencha Touch
---

1. [iOS7 Phonegap / Cordova statusbar fix](http://abitofcoding.blogspot.ch/2014/01/ios7-phonegap-layout-fix.html)

2. 适配retina屏


```
	#yourimage {
		height: 100px;
		width: 320px;
		-webkit-background-size: 320px 100px; // Critical line! 
		background: url("images/yourimage.png") no-repeat;
	}

// This detects the iPhone 4 Retina display
	@media only screen and (-webkit-min-device-pixel-ratio: 2) {
		#yourimage {
			background: url("images/yourimage@2x.png") no-repeat;
		}
	}
```
3.创建实例的方式有两种`Ext.create()`和new,不过建议用Ext.create，这样可以用到动态加载的好处

4.NestedList的方法中判断是哪一个record(用tpl)


```javascript

getItemTextTpl: function(record) {

	return '<tpl if="text==\'Fruit\'">{text}</tpl>{' + this.getDisplayField() + '}';
}
```
5.本地资源编译到device上无法加载的问题
需要在app.json的resources数据里加上本地资源的路径!
6.Ext.widget() 可以用xtype 来创建实例!
7.工具集(Foundation Utilities)

