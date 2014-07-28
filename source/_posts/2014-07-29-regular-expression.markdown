---
layout: post
title: "Regular Expression"
date: 2014-07-29 12:59:58 +0800
comments: true
categories: Ruby
---
正则表达式如下	

	/cat/
	
	/123/
	
正则表达式`/.../`里的常量和双引号的字符串类似,实际上，你可以在正则里，你也可以用`#{...}`表达式的替换形式

#####Matching Strings with Patterns

	/cat/ =~ "dog and cat" # => 8 
	
	/cat/ =~ "catch" # => 0 
	
	/cat/ =~ "Cat" # => nil

如果你喜欢也可以把字符串放在前面

	"dog and cat" =~ /cat/ # => 8 
	
	"catch" =~ /cat/ # => 0 
	
	"Cat" =~ /cat/ # => nil
	
模式匹配如果失败，会返回nil、，而nil在条件表达式中相当于false，所以可以把正则表达式的匹配结果作为if和while的条件语句

	str = "cat and dog"
		if str =~ /cat/
	puts "There's a cat here somewhere"
	end
	produces:
	There's a cat here somewhere

以下的代码打印"testfile"文件中有"on"字符串的行

	File.foreach("testfile").with_index do |line, index| 
	
	puts "#{index}: #{line}" if line =~ /on/
		end
	produces:
	0: This is line one
	3: And so on...你可以用!~来查找`不匹配`的字符串

	File.foreach("testfile").with_index do |line, index| 
	
		puts "#{index}: #{line}" if line !~ /on/
			end
	produces:
	1: This is line two
	2: This is line three

#####Changing Strings with Patterns
sub方法用来查找并替换匹配的字符串

	str = "Dog and Cat"
		new_str = str.sub(/Cat/, "Gerbil")
	puts "Let's go to the #{new_str} for a pint."
	produces:
	Let's go to the Dog and Gerbil for a pint.

sub只是替换第一个匹配的，要替换全局匹配的，就用gsub

	str = "Dog and Cat"
		new_str1 = str.sub(/a/, "*") 
	new_str2 = str.gsub(/a/, "*") 
	puts "Using sub: #{new_str1}" 
	puts "Using gsub: #{new_str2}"
	produces:
	Using sub: Dog *nd Cat
	Using gsub: Dog *nd C*t	
需要注意的是，sub和gsub都返回一个全新的string,不论是否替换！如果替换，也返回原string的一个copy
如果你想要修改原始的string，你可以用sub!和gsub!的形式
	
	str = "now is the time" 
	
	str.sub!(/i/, "*") 
	
	str.gsub!(/t/, "T") 
	
	puts str
		produces:
	now *s The Time
不过不像sub和gsub，sub!和gsub!只有在匹配正则表达式时才返回string，如果不匹配，则返回nil，这意味着你可以将它放在条件语句中
#####Digging Deeper
未完待续....
	