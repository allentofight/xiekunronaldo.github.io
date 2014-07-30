---
layout: post
title: "Fibers Threads Processes"
date: 2014-07-30 15:42:25 +0800
comments: true
categories: Ruby
---
Ruby gives you two basic ways to organize your program so that you can run different parts of it apparently “at the same time.” Fibers let you suspend execution of one part of your program and run some other part. For more decoupled execution, you can split up cooper- ating tasks within the program, using multiple threads, or you can split up tasks between different programs, using multiple processes. Let’s look at each in turn.####Fibers
Ruby 1.9引入了fibers的概念,尽管名字意味着某种轻量级的线程，实际上，fibers仅仅是一种非常简单的coroutine机制，它能让你写一些看起来用了线程的程序，实际上没有引发任何线程中内在的复杂机制
计算一个文件中单词的出现次数，不用fibers版本
	counts = Hash.new(0) 
	File.foreach("testfile") do |line|
		line.scan(/\w+/) do |word| 
		word = word.downcase 
		counts[word] += 1
		end 
	end
	counts.keys.sort.each {|k| print "#{k}:#{counts[k]} "} 
	produces:
	and:1 is:3 line:3 on:1 one:1 so:1 this:3 three:1 two:1这段代码非常的ugly,它混合了查找word和计算word两种逻辑,来看看用Fiber的方法
	words = Fiber.new do 
		File.foreach("testfile") do |line|			line.scan(/\w+/) do |word| 
			Fiber.yield word.downcase
			end 
		end		nil	￼end
	counts = Hash.new(0)
	while word = words.resume 
		counts[word] += 1
	end
	counts.keys.sort.each {|k| print "#{k}:#{counts[k]} "} 
	produces:
	and:1 is:3 line:3 on:1 one:1 so:1 this:3 three:1 two:1
再来看一个demo
	twos = Fiber.new do 
		num = 2		loop do			Fiber.yield(num) unless num % 3 == 0 
			num += 2
			end 
		end	10.times { print twos.resume, " " } 
	produces:
	2 4 8 10 14 16 20 22 26 28

以上也可以用之前提到的lazy Enumberators中实现由于fibers也是对象，你可以将它们存储在变量中，并且fibers只能在创建它们的线程中resume
fibers can yield control only back to the code that resumed them,不过Ruby提供了两种标准库来拓展这种行为fiber库和continuation库

####多线程

