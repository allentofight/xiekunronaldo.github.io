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

UIView的这个方法可以实现live-rendering,注意要与@IBDesignable配合使用，如果要使用UIImage,则需用以下方式

```
            let bundle = NSBundle(forClass: self.dynamicType)
            self.image = UIImage(named: "sex_girl1", inBundle: bundle, 			compatibleWithTraitCollection: self.traitCollection)
```
UIImage(named:)无效!

*Quicklook Preview

Model实现debugQuickLookObject即可

*Size Class
Size classes have vertical and horizontal dimensions called "regular" and "compact". The iPad in both portrait and landscape defaults to the regular size class in both horizontal and vertical directions. The iPhone in portrait defaults to compact size class for horizontal and regular size class for vertical. The iPhone in landscape defaults to compact size class for both horizontal and vertical.




