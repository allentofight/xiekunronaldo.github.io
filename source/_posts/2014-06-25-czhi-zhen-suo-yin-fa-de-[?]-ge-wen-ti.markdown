---
layout: post
title: "C指针所引发的一个问题"
date: 2014-06-25 21:39:33 +0800
comments: true
categories: Head First C笔记
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