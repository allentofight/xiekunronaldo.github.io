---
layout: post
title: "初识Ruby"
date: 2014-07-21 22:23:15 +0800
comments: true
categories: Ruby
---
在终端中输入irb与ruby交互

		2.1.2 :002 > def sum(n1, n2)
		2.1.2 :003?>   n1+n2
		2.1.2 :004?>   end
		 => :sum
		2.1.2 :005 > sum(3, 4)
		 => 7
通常我们将ruby程序写在文件里，假设现在有一个myprog.rb的程序如下

		gettingstarted/myprog.rb		puts "Hello, Ruby Programmer" 		puts "It is now #{Time.now}"
现在我们可以敲入以下命令来运行程序

		$ ruby myprog.rb		Hello, Ruby Programmer)		It is noew 2014-07-21 22:27:47 +0800
在Unix中，我们可以在程序的第一行添加"shebang"的符号，如下

		#! /usr/bin/ruby
		puts "Hello, Ruby Programmer" 
		puts "It is now #{Time.now}"
如果你用`chmod+x myprog.rb`命令让此文件成为可执行程序，Unix能让你将此文件作为一个程序来运行

		$ ./myprog.rb		Hello, Ruby Programmer		It is now 2013-05-27 12:30:36 -0500####Ruby文档:RDoc和ri
Ruby库越来越大，已经不可能将有关它们的文档写进一本书中了，ruby的标准库有9000多个！幸运的是，一种为这些方法(类，模块)提供文档的方法是存在的，许多在它们内部写文档的方法是采用一个RDoc的工具
如果一个源文件用RDoc来文档注释，那么它的文档是可以被提取出来并被转化成HTML和ri的形式的
ri是一个浏览本地RDoc的，命令行浏览器，许多ruby的发行版现在用ri程序来安装文档资源
要发现针对某个类的文档，我们可以在命令行敲入`ri ClassName`的形式来查看,如以下
	$ ri GC
	----------------------------------------------------------------------------------- 
	The GC module provides an interface to Ruby's garbage collection mechanism. Some of the underlying 	methods are also available via the ObjectSpace module.           You may obtain information about the operation of the GC through GC::Profiler.    -----------------------------------------------------------------------------------    Class methods:      count, disable, enable, malloc_allocated_size, malloc_allocations,      start, stat, stress, stress=    Instance methods:      garbage_collect
查看某个指定类的方法
	ri GC::enable
	--------------------------------------------------------------- GC::enable GC.enable => true or false 	--------------------------------------------------------------------------- Enables garbage collection, returning true if garbage collection was 	disabled.	GC.disable	GC.enable	GC.enable	#=> false	#=> true	#=> false	