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