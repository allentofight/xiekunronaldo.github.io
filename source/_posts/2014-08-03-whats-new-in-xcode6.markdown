---
layout: post
title: "Whats new in xcode6"
date: 2014-08-03 15:35:28 +0800
comments: true
categories: wwdc2014
---
Storyboard中能实时看到Custom Fonts

{% img /images/2014/8/customfont.png%}

swift如需使用不在某文件中定义的类，不需要import,会自动import，不过，你依然需要为你信赖的framework import modules,
创建.swift文件时，默认创建了.h头文件，所以如果在.m文件中引用swift文件中的类，可以用import .swift对应的头文件来引用
在ios8中，可以@import第三方的framework了!
#####在IB中动态改变显示效果，如动态改变圆角
注意以下几个方法的使用
`class`前写@IBDesignable,属性前写@IBInspectable,还有override以下方法

@override func prepareForInterfaceBuilder(){}

`Size Classes`



