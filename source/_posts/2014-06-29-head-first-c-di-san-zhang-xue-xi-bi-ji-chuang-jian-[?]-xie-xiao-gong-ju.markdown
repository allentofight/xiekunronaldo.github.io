---
layout: post
title: "Head First C 第三章学习笔记-创建小工具"
date: 2014-06-29 18:44:13 +0800
comments: true
categories: C语言
---
###要点
Linux/Unix系统有很多小工具,这些小工具执行一些特定的任务，比如读写文件，过滤数据等，这些工具都只负责处理自己的事情，各司其职，无需顾虑其他的工具所做的事，这样做的好处可以如果你需要处理一个比较复杂的任务，你可以将它分割成各个小任务，每个小任务由相应的工具完成，就有效地做到了解耦

此章介绍我们将学习如何了解命令行参数，处理流信息，重定向，并快速地将各个工具组合起来

本章我们主要以将以下数据格式

	42.363327,-71.097588,Speed = 23 
	42.363255,-71.096710,Speed = 17
转换成以下的JSON形式进行讨论分析

	[{latitude: 42.363400, longitude: -71.098465, info: 'Speed = 21'},
	 {latitude: 42.363327, longitude: -71.097588, info: 'Speed = 23'}, 
	 {latitude: 42.363255, longitude: -71.096710, info: 'Speed = 17'},	...]

以下程序(geo2json)能从命令行中读取以逗号分隔的行，并将它以JSON的形式展示出来

	//geo2json
	#include <stdio.h>	int main() {		float latitude;		float longitude;		char info[80]; intstarted= 0 ￼ ￼ ;		puts("data=["); 		while (scanf("%f,%f,%79[^\n]", &latitude, &longitude, info) == 3) {			if (started) 				printf(",\n");			else				started= 1 ;
			if ((latitude < -90.0) || (latitude > 90.0)) { 				printf(“Invalid latitude: %f\n”, latitude);				return 2;
			}			if ((longitude < -180.0) || (longitude > 180.0)) {				printf(“Invalid longitude: %f\n”, longitude);				return 2; 			}			printf("{latitude: %f, longitude: %f, info: '%s'}", latitude , longitude , info );		}		puts("\n]");		return 0; 	}


scanf()会返回输入的参数个数

	while (scanf("%f,%f,%79[^\n]", , , ) == 3)
	
* 注意以上的[^\n]表示第三个输入参数的内容为除换行以外的字符串

以上程序让你在键盘上输入GPS数据，将将它们在JSON的形式输出到屏幕上,问题是输入和输出混起来了，如果有许多的数据，你肯定不想在键盘上手动输入，你肯定希望能用一个小工具(geo2json)将大量的数据通过读取文件来获取.

在以上的程序中，你用scanf()和printf()来从键盘中读取数据并将它显示到显示器上，但实际上它们并不直接和键盘和显示器交互，它们用了标准输入(Standard Input)和标准输出(Standard Output)来和相应的输入输出设备交互,输入输出在程序运行的时候由操作系统创建

操作系统决定了数据如何进出标准输入输出的，如果你从终端运行一个程序，操作系统将把所有的按键信息发送给标准输入，如果操作系统从标准输出中获取数据，默认情况下它将把数据输入出显示器上
scanf()和printf()两个函数并不关心数据来自哪里，去向何方，它们从标准输入和标准输出中读写数据
可能你会问，为何这两个函数不直接和键盘，屏幕交互，难道不是更简单吗

程序系统用标准输入和标准输出的形式与程序进行交互有一个很好的理由:
你可以重定向标准输入和标准输出到其他地方，比如文件

你可以用"<"来重定向标准输入:

	./geo2json < gpsdata.csv
	
"<"告诉操作系统程序的标准输入应与gpsdata.csv文件连接，而非键盘，所以你可以通过文件向程序传送数据

你也可以用">"来重定向标准输出

	./geo2json < gpsdata.csv > output.json
将你执行以上命令，你将发现在屏幕上没有任何数据，因为结果已经输出到output.json这个文件去了

以上的程序(geo2json)虽然加了错误判断，但如果有非法数据，在最后的输出文件中，一样会有非法数据
如果发生错误，如何发现错误码(以上为2)
	
	echo $?
如果数据错误了，能有专门针对这些错误的输出该多好!
标准错误(Stand Error)应运而生!它就是专门针对错误信息的输出，默认情况下，标准错误与屏幕相连,以下是默认状态下的三种标准对应设备的图

 {% img left /images/2014/6/standardIOE.png 350 350 'image' %}  
 
 
 也就是说如果重定向了标准出入和标准输出，标准错误仍将发送数据到屏幕上
 
 {% img left /images/2014/6/standardIOE2.png 350 350 'image' %}
 

 
