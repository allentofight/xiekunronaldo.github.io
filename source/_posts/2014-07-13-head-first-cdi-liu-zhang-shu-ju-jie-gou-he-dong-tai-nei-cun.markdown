---
layout: post
title: "Head First C第六章-数据结构和动态内存"
date: 2014-07-13 11:19:10 +0800
comments: true
categories: C语言
---
这一章中我们将学习到如何用结构体指针来将自定义的数据类型连接成一个更大更复杂的数据结构，我们通过创建链表来了解结构体指针的用处，也将学习到如何在堆上分配内存，并在你用完之后释放它，我们也将学习到valgrind的使用
####在堆上分配内存以及释放内存

	typedef struct island{ 
		char *name;
		char *opens;
		char *closes;
		island *next;	} island;
	
	#include<stdlib.h>
	island *p = malloc(sizeof(island))
	free(p);
以下函数有何问题

	island* create(char *name) {		island *i = malloc(sizeof(island)); 		i->name = name;		i->opens = "09:00";		i->closes = "17:00";		i->next = NULL;		return i; 
	}
