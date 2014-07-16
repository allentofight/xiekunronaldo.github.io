---
layout: post
title: "Head First C第六章-数据结构和动态内存"
date: 2014-07-13 11:19:10 +0800
comments: true
categories: C语言
---
这一章中我们将学习到如何用结构体指针来将自定义的数据类型连接成一个更大更复杂的数据结构，我们通过创建链表来了解结构体指针的用处，也将学习到如何在堆上分配内存，并在你用完之后释放它，我们也将学习到valgrind的使用
####在堆上分配内存以及释放内存
```
	typedef struct island{ 
		char *name;
		char *opens;
		char *closes;
		island *next;	} island;
	
	#include<stdlib.h>
	island *p = malloc(sizeof(island))
	free(p);
```
以下函数有何问题

```
	island* create(char *name) {
			island *i = malloc(sizeof(island)); 
		i->name = name;
		i->opens = "09:00";
		i->closes = "17:00";
		i->next = NULL;
		return i; 
	}
```

当在程序里调用如下代码时

```
char name[80];fgets(name, 80, stdin);island *p_island0 = create(name); 
fgets(name, 80, stdin);island *p_island1 = create(name);
```

p_island0和p_island1的name是一样的！！！原来是create函数传入的只是name的地址，两者的地址是一样的，你必须用strdup函数copy字符串,故以上create函数的代码中的

	i->name = name;

可改为

	i->name = strdup(name);
不过要记得用strdup()拷贝字符串，最后需要用free()将此拷贝字符串release掉
###valgrind可用来检测内存泄漏
valgrind能模拟在堆上分配的数据，它通过创建模拟的malloc()版本来工作，当你的程序想要分配一些堆上的内存时，valgrind将截获你对malloc()和free()的调用并运行它自己的模拟版本的malloc()和free(),valgrind版本的malloc()将记录调用它的那些代码以及它所分配的那部分内存,当你的程序终止时,valgrind将会报告任何堆上还存在的数据并且告诉你这些数据由哪些代码调用产生的
####准备:添加debug信息
用valgrind运行之前你不需要对你的代码作任何操作，你甚至不需要重新编译代码，但是为了更好地利用valgrind，代需要确保你的可执行代码包含debug信息，debug信息是在编译代码后嵌入到可执行文件里的额外数据，比如代码对应的行数，如果debug信息存在了，valgrind将能给你许多内存泄漏的具体信息
测试所用程序[spies](https://gist.github.com/xiekunRonaldo/cdde073cac5636270322)
用valgrind结合--leak-check=full选项进行以下测试

{% img /images/2014/7/valgrind1.png%}

从上图可以看出，当SPIES退出后，堆上已无对象，再运行一遍试试看，然后告诉程序有一个Hayden Fantucci的嫌疑人会怎样

{% img /images/2014/7/valgrind2.png%}

这次,valgrind发现了内存泄漏,从上图可看出，在程序结束时，堆上有19个字节的空间未释放,valgrind告诉你以下信息

1. 19个字节的内存分配了但未释放
2. 分配了11次的内存空间，但只释放了10次
3. 这几行给了我们什么线索
4. 为什么是19个字节？说明了什么?
从以个信息可得,"Loretta Barnsworth"占了19个字节,strdup导致了内存泄漏！
* valgrind是如何截获对malloc()和free()的调用的
答:malloc()和free() 实际上是标准库函数，不过valgrind包含一个它自己的malloc()和free()版本的标准库函数，当你用valgrind来运行程序时，你的程序将用valgrind的malloc()和free()函数，而非C的标准库函数


