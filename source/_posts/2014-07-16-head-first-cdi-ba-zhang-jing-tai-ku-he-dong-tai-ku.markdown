---
layout: post
title: "Head First C第八章-静态库和动态库"
date: 2014-07-16 21:54:55 +0800
comments: true
categories: 
---
这一章中，我们将学习如何创建自己的动态库，并在多个程序中用到同样的代码
来看下以下代码
加密代码

	//encyrpt.h
	void encrypt(char *message)
	
	//encrypt.c
	#include "encrypt.h"	void encrypt(char *message) {		while (*message) { 			*message = *message ^ 31; message++;		}	 }
校验代码
	
	//checksum.h
	int checksum(char *message);
	
	//checksum.c
	#include "checksum.h"	int checksum(char *message) {		int c = 0;		while (*message) {		c += c ^ (int)(*message);		message++; }		return c; 
	}
加密和校验两段代码构成了我们接下来将要进行的密码库的基础
假设我们已经将以上的代码编译成了目标代码

	gcc -c encrypt.c -o encrypt.o
	gcc -c checksum.c -o checksum.o
现在我们写一段测试程序

	#include <stdio.h> 
	#include <encrypt.h> 
	#include <checksum.h>	int main() {		char s[] = "Speak friend and enter"; encrypt(s);		printf("Encrypted to '%s'\n", s); printf("Checksum is %i\n", checksum(s)); encrypt(s);		printf("Decrypted back to '%s'\n", s); printf("Checksum is %i\n", checksum(s));		return 0;	}
当我们编译时，会出现以下错误

	gcc test_code.c encrypt.o checksum.o -o test_code 
	test_code.c:2:21: error: encrypt.h: No such file or directory 
	test_code.c:3:22: error: checksum.h: No such file or directory
	
错误在以下两行

	#include <encrypt.h> 
	#include <checksum.h>
尖括号将在标准头文件目录中寻找，在Mac或Linux系统中，编译器在以下目录中寻找这些文件
	
	/user/local/include		//此目录下通常存放一些第三方库
	/user/include			//此目录通常存放一些操作系统相关的头文件
####该如何共享代码
有时你想写一些代码用在很多程序中，无论它们是在不同的文件夹下，还是在整个电脑中，你该如何做呢?
一般来说我们想在多个程序中共享两种文件,头文件(.h)和目标文件(.o),让我们看看如何操作

#####共享.h头文件,有以下几种文件

1. 将它们存储在标准目录中
如果你将你的头文件拷贝到标准目标如/usr/local/include中，你可以用尖括号来引用它们

		#include<encrypt.h>

2. 在include中写下此文件所在的完整路径
假设你的文件存储在/my_header_files目录下,你可以按以下方式来引用代码
	
		#include "/my_header_files/encrypt.h"
3. 你可以告诉编译器去哪来搜寻它们
用gcc编译时加上-I选项

		gcc -I /my_header_files test_code.c ... -o test_code
-I选项告诉gcc编译器在/my_header_files这个目录下你能找到所需要的头文件，`注意`，gcc仍将在标准目录中寻找头文件，但是`首先`它将在-I选项对应的目录中寻找此文件
#####通过写完整路径的方式来共享.o目标文件

		gcc -I/my_header_files test_code.c /my_object_files/encrypt.o /my_object_files/checksum.o -o test_code
/my_object_files目录就像是存储目标文件的中枢,用目标文件的完整路径意味着你不需要为每一个程序拷贝一份单独的目标文件
如果只有一两个需要共享的目标文件还好，如果有许多目标文件呢，不是要在所有的目标文件前都加上它们的完整路径了?
`解决方法`:你可以将许多目标文件打包成一个.a文件,这样你一次就能告诉编译器所有目标文件的所在了(都在.a文件里)
######让我们看下如何打包
我们用过.zip和.tar文件，这些文件包含了多个文件,.a文件与此类似，一个文件包含了多个.o文件
打开命令行，切换到库目录下，比如/usr/lib里。在此目录上，你将看到许多.a文件,有一个nm的命令可以用来查看.a文件里有哪些.o文件
{% img /images/2014/7/libl.png%}
{% img /images/2014/7/nm.png%}
在我们用.a文件编译程序之前,先看看如何将encrypt.o和checksum.o文件存储到一个.a文件里吧
用ar命令来创建一个.a文件
	
		ar -rcs libhfsecurity.a encrypt.o checksum.o
rcs三个选项的解释如下
-r表示如果(.a)文件已经存在,文件将被更新
-c表示.a创建成功与否都无任何反馈信息
-s告诉ar在文件的开头创建一个索引
注意到了吗，所有的.a文件都有lib<something>.a形式的命名,名字以lib开头，因为它们是静态库，我们将很快学习到什么是静态库

创建好了标准库文件后，该如何使用呢

* 将它放到一个标准目录如/user/local/lib里
  一些码农在确认.a文件能工作后喜欢将.a文件放在一标准库里，在Linux和Mac里/usr/local/lib目录是一个很好的选择，因为这个目录就是专门用来放置你自己的库文件的
  
* 将.a文件放到你自己喜欢放置的库文件目录下,如/my_lib中
既然库创建好了，那我们来看看怎么利用这些库来编译用到这些库的程序
1. 如果你将.a文件放到标准库目录中,你可以按以下方式来编译代码
{% img /images/2014/7/nm.png%}	
2. 如果你是将.a文件放在你自己的库文件目录下，可按以下方式来编译代码

		gcc test_code.c -L/my_lib -lhfsecurity -o test_code
-L选项告诉编译器去哪个目录寻找库文件

现在有一个搞安全的哥们在用最新的加密库编译一个银行相关的程序时遇到了一点麻烦，他把源码和 ecnrypt，checksum 的源码放在了同一个目录下，他想要在同一个目录下创建一个libhfsecurity.a库并用它去编译程序，你能帮助它修改它的makefile文件吗
这个程序叫band_vault，它用了以下的include语句 
	
	#include<encrypt.h>
	#include<checksum.h>
由于用到了尖括号，编译器需要使用用-I选项告知头文件在哪
makefile文件如下
{% img /images/2014/7/bank_vault_makefile.png%}	
-L选项必须出现在源文件后面才行
从.a文件中提取中某个目标文件

	ar -x libhfsecurity.a encrypt.o
再来看一个例子
	
	//hfcal.c
	#include <stdio.h> 
	#include <hfcal.h>
	
	void display_calories(float weight, float distance, float coeff) {		printf("Weight: %3.2f lbs\n", weight);		printf("Distance: %3.2f miles\n", distance);		printf("Calories burned: %4.2f cal\n", coeff * weight * distance);	}
	
	//elliptical.c
	#include <stdio.h>
	#include <hfcal.h>	int main() {		display_calories(115.2, 11.3, 0.79);		return 0; 	}
下面我们来看看如何编译代码

1. 首先，创建hfcal.o文件,hfcal.h头文件存储在./includes

		gcc -I./includes -c hfcal.c -o hfcal.o
-c选项表示只是创建目标文件，不链接
2. 创建elliptical.o文件
	
		gcc -I./includes -c elliptical.c -o elliptical.o
同样的，你需要告诉编译器头文件在./includes下的
3. 你需要从hfcal.o中创建一个.a文件，并将它存储在./libs中
		
		ar -rcs ./libs/libhfcal.a hfcal.o

被打包的libhfcal.a文件将被放在./libs目录下

4. 最后，用elliptical.o和libhfcal.a创建elliptical可执行文件

		gcc elliptical.o -L./libs -lhfcal -o elliptical

现在你能执行elliptical可执行文件了

	> ./elliptical	Weight: 115.20 lbs 	Distance: 11.30 miles 	Calories burned: 1028.39 cal 	>






