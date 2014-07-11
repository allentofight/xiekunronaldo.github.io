---
layout: post
title: "Head First C第五章学习笔记"
date: 2014-07-13 09:22:09 +0800
comments: true
categories: C语言
---
####Union的初始化
1. 用.来初始化

		typedef union { 
			short count;
			float weight;
			float volume;		} quantity;
		//By name		quantity q = {.weight=1.5};
		//By dot notation
		quantity q;
		q.volume = 3.7;
	
2. struct也可以按名字(Designated initializer)来进行初始化
			typedef struct { 			const char *color;			int gears;			int height;		} bike;		bike b = {.height=17, .gears=21};
		
union可以用在struct中

	typedef struct {		const char *name;		const char *country;		quantity amount;	} fruit_order;
	
你可以通过.或->来访问成员值

	fruit_order apples = {"apples", "England", .amount.weight=4.2};
假设有一下定义

	typedef union { 
		float lemon;
		int lime_pieces;	} lemon_lime;
	typedef struct {	 	float tequila;	 	float cointreau;	 	lemon_lime citrus;	} margarita;
则以下定义是错误的
	
	margarita m;	m = {2.0, 1.0, {0.5}};  //编译器会认为是数组
以下定义才是对的

	margarita m = {2.0, 1.0, {0.5}};
在一个union里，可能会有很多可能的值，但一旦被赋值，你没法知道它存储的值的类型
我们可能为某些字段设定了值却访问了另一个字段，这可能出现大问题,假设有以下程序

	#include <stdio.h> 
	typedef union { 
	  float weight;	  int count;	} cupcake;
		int main() {		cupcake order = {2};		printf("Cupcakes quantity: %i\n", order.count);		return 0;	}
运行程序将会出现以下结果

	gcc badunion.c -o badunion && ./badunion 
	Cupcakes quantity: 1073741824可以用enum来避免此类问题
####Bitfiled
bitfield能让你指定struct里的field能有几位，你可以按如下方式定义struct

	typedef struct {
		unsigned int low_pass_vcf:1;
		unsigned int filter_coupler:1;	} synth;
用bitfield，你能让每个field都只占一位
如果你有一连串的bitfields,计算机能将它们排列压缩以节省空间，所以如果你有8个1位的bitfields，电脑将会把它们存储到一个字节里
bitfield的不仅可以节省空间，也可以读写某些二进制文件

* bitfield应该被声明为unsigned int
