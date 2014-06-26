---
layout: post
title: "C指针所引发的一个问题"
date: 2014-06-25 21:39:33 +0800
comments: true
categories: C语言
---


	void fortune_cookie(char msg[]) {
	     printf("Message reads: %s\n", msg);
	     printf("msg occupies %i bytes\n", sizeof(msg)); 
	} 
	
	int main () {
		char msg[] = "Turtles!";
		fortune_cookie(msg);
		return 0;
	}
	
运行以上代码会输出什么,有人说是

	Message reads Turtles!
	msg occupies 9 bytes("Turtles!"的8个字节再加代表字符串结尾的\0这个字节，所以是9个字节)
	
如果你这么想，那就错了,实际的结果是

	Message reads Turtles!
	msg occupies 4 (or 8) bytes

解释如下

## 数组变量就像指针一样

是的，只是像，而不等于指针

当你创建了一个数组,数组变量其实可以被当作指向数组首元素地址的指针,当写了如下代码时

	char quote[] = "Cookies make you fat";
	
实际上quote代表的是string中的首字符的地址，即字符'C'对应的地址

	printf("The quote string is stored at: %p\n", quote);
	
以上将打印一个16进制的地址,尽管quote是一个数组，但是你可以把"quote"当成一个指针变量，因为它本身就是代表首字符串中的首字符的地址.

所以当调用

	fortune_cookie(msg);
	
时，其实我们给fortune_cookie函数传的是一个指针,它返回的是指向一个字符串的指针的大小，在32位上操作系统，一个指针占了4个字节的空间，而在64位上，一个指针则占据了8个字节,故有以上结果

## 数组变量不是指针

尽管在调用函数时，数组参数是可以被当作指针传递，但实际上仍有一些不同，考虑以下情况

	char s[] = "How big is it?"; 
	char *t = s;
	
1. sizeof(数组) 是一个数组的大小.
	sizeof(s) = 15   sizeof(t) = 4 或 8
2. 数组的地址 
	&s = s     &t != t
如果你写&s,表示数组s的地址是什么,数组s的地址就是s,&t表示t变量的地址，显然不等于t(表示s的地址)
3. 一个数组变量不能指向任何地方
	如果你创建了一个指针，机器将会为之分配4或8个字节来存储它,但如果你创建了一个数组，机器将为此`数组`分配空间，而不分为此`数组变量`分配**任何**的空间,数组变量仅代表数组的首地址
	
	由于数组变量没有任何的存储空间，所以它不能指向任何地方,当程序编译时，所以的数组变量都被数组的地址替换了，所以数组变量并不存在最后的可执行文件中，这也就是为什么数组变量不能指向任何其他地方的原因
	
## 指针退化(Pointer decay)

由于数组变量和指针变量有些区别，当你把数组赋给指针时需要特别小心，如果你将一个数组赋给一个指针变量，此时的指针变量将只包含数组的地址,没法知道数组的大小，所以一些信息丢失了，这种丢失信息的情况就叫做指针退化
