---
layout: post
title: "Basic Input and Output"
date: 2014-07-30 14:27:07 +0800
comments: true
categories: Ruby
---
两个I/O routines
第一个是基本的交互界面

	print "Enter your name: " 

	name = gets
一系列I/O相关的方法在Kernel module里实现gets, open, print, printf, putc, puts, readline, readlines, 和 test,这些方法通常都与标准输入，标准输出有关
第二个则是与IO对象交互
IO类，有两个子类File和BasicSocket
#####开头文件
	file = File.new("testfile", "r") 
	# ... process the file 
	file.close

File与Block
	File.open("testfile", "r") do |file| 
		# ... process the file
	end # <- file automatically closed here与exception结合
	class File
		def File.open(*args)
			result = f = File.new(*args) 
				if block_given?
					begin
						result = yield f
					ensure
						f.close
					end 
				end
				result
				end 
	end

#####读写文件
来看一段copy.rb代码

	while line = gets 

		puts line
	end

如果运行程序木有命令行参数，则与console交互

	$ ruby copy.rb 

	These are lines 

	These are lines 

	that I am typing 

	that I am typing 

	^D
如果带文件名，则与文件交互

	$ ruby copy.rb testfile 

	This is line one
	This is line two
	This is line three
	And so on...
我们可以用File类来读写文件
	File.open("testfile") do |file| 
		while line = file.gets			puts line		end 
	end
		produces:
	This is line one
	This is line two
	This is line three
	And so on...
#####Iterators for Reading
你可以用各种iterators与IO流交互
O#each_byte invokes a block with the next 8-bit byte from the IO object,chr则将integer转化为ASCII字符
	File.open("testfile") do |file| 
		file.each_byte.with_index do |ch, index|
			print "#{ch.chr}:#{ch} "
			break if index > 10 
		end
	end
	produces:
	T:84 h:104 i:105 s:115  :32 i:105 s:115  :32 l:108 i:105 n:110 e:101
IO#each_line
	File.open("testfile") do |file|
		file.each_line {|line| puts "Got #{line.dump}" }
	end
	produces:
	Got "This is line one\n"
	Got "This is line two\n"
	Got "This is line three\n"
	Got "And so on...\n"
你也可以将一串字符传给each_line来作为一个line separator,默认是\n
	File.open("testfile") do |file|
		file.each_line("e") {|line| puts "Got #{ line.dump }" }
	end
	produces:
	Got "This is line"
	Got " one"
	Got "\nThis is line"
	Got " two\nThis is line"
	Got " thre"
	Got "e"
	Got "\nAnd so on...\n"

combine iterator和autoclosing block,我们得到了IO.foreach,这个方法打开文件，调用iterator一次一行，然后自动关闭文件

	IO.foreach("testfile") {|line| puts line }将整个文件文件的内容保存在一个string或每行保存在一个数组里
	//保存在string中
	# read into string
	str = IO.read("testfile")
	str.length # => 66
	str[0, 30] # => "This is line one\nThis is line "
	
	//保存在array中
	
	# read into an array	arr = IO.readlines("testfile") 
	arr.length # => 4
	arr[0] # => "This is line one\n"
####写入文件
	# Note the "w", which opens the file for writing
	File.open("output.txt", "w") do |file| 
	file.puts "Hello"
	file.puts "1 + 2 = #{1+2}"
	end
	# Now read the file in and print its contents to STDOUT
	puts File.read("output.txt")
	produces:
	Hello 1+2=3

如果将二进制数据转为string,有以下三种方法

	str1 = "\001\002\003" # => "\u0001\u0002\u0003" 

	str2 = ""
	str2 << 1 << 2 << 3 # => "\u0001\u0002\u0003" 
	[ 1, 2, 3 ].pack("c*") # => "\x01\x02\x03"
你也可以用<<将对象添加到输出流中
	endl = "\n"
	STDOUT << 99 << " red balloons" << endl
	produces:
	99 red balloons	

<<方法在打印前将用to_s将它的参数转为string
#####Doing I/O with StringsStringIO对象，它们操作起来就像I/O对象，但它们只是读写strings，而不是files
	require 'stringio'
	ip = StringIO.new("now is\nthe time\nto learn\nRuby!")
	op = StringIO.new("", "w")
	ip.each_line do |line| 
		op.puts line.reverse
	end
	op.string # => "\nsi won\n\nemit eht\n\nnrael ot\n!ybuR\n"
####Talking to Networkds
socekt库封装了TCP,UDP,SOCKS和Unix domainsockes	require 'socket'	client = TCPSocket.open('127.0.0.1', 'www') 
	client.send("OPTIONS /~dave/ HTTP/1.0\n\n", 0) 
	puts client.readlines
	client.close
	produces:
	# 0 means standard packet
	HTTP/1.1 200 OK
	Date: Mon, 27 May 2013 17:31:00 GMT
	Server: Apache/2.2.22 (Unix) DAV/2 PHP/5.3.15 with Suhosin-Patch mod_ssl/2.2.22
	OpenSSL/0.9.8r
	Allow: GET,HEAD,POST,OPTIONS
	Content-Length: 0
	Connection: close
	Content-Type: text/html
在高层,lib/net library modules提供了对通信协议的许多支持(FTP, HTTP, POP, SMTP, and telnet)

	//以下只是一部分代码
	require 'net/http'
	http = Net::HTTP.new('pragprog.com', 80)
	response = http.get('/book/ruby3/programming-ruby-1-9')
	if response.message == "OK"
	puts response.body.scan(/<img alt=".*?" src="(.*?)"/m).uniq[0,3]
	end
	produces:
	       http://pragprog.com/assets/logo-c5c7f9c2f950df63a71871ba2f6bb115.gif
	       http://pragprog.com/assets/drm-
	       free80-9120ffac998173dc0ba7e5875d082f18.png
	       http://imagery.pragprog.com/products/99/ruby3_xlargecover.jpg?
	       1349967653open-url库中的Object#open方法能识别文件中的http://和ftp://URLs,也能自动处理重定向 
	require 'open-uri'
	open('http://pragprog.com') do |f|
	puts f.read.scan(/<img alt=".*?" src="(.*?)"/m).uniq[0,3]
	end
	produces:
	       http://pragprog.com/assets/logo-c5c7f9c2f950df63a71871ba2f6bb115.gif
	       http://pragprog.com/assets/drm-free80-9120ffac998173dc0ba7e5875d082f18.png
	       http://imagery.pragprog.com/products/353/jvrails2_xlargebeta.jpg?1368826914
####Parsing HTML
	require 'open-uri'
	page = open('http://pragprog.com/titles/ruby3/programming-ruby-1-9').read 
	if page =~ %r{<title>(.*?)</title>}m
		puts "Title is #{$1.inspect}" 
	end
	produces:
	       Title is "The Pragmatic Bookshelf | Programming Ruby 1.9"
但用此法不太妥当，可能title和尖括号间多个个空格如何是好，可以用第三方库Nokogiri
	require 'open-uri' 
	require 'nokogiri'
	doc = Nokogiri::HTML(open("http://pragprog.com/"))
	puts "Page title is " + doc.xpath("//title").inner_html
	# Output the first paragraph in the div with an id="copyright" 
	# (nokogiri supports both xpath and css-like selectors)
	puts doc.css('div#copyright p')
	# Output the second hyperlink in the site-links div using xpath and css
	puts "\nSecond hyperlink is"
	puts doc.xpath('id("site-links")//a[2]') puts doc.css('#site-links a:nth-of-type(2)')
	produces:
	Page title is The Pragmatic Bookshelf
	<p>
	    The <em>Pragmatic BookshelfTM</em> is an imprint of
	        <a href="http://pragprog.com/">The Pragmatic Programmers, LLC</a>.
	    <br>
	    Copyright © 1999–2013 The Pragmatic Programmers, LLC.
	    All Rights Reserved.
	</p>
	Second hyperlink is
	<a href="http://pragprog.com/about">About Us</a>
	<a href="http://pragprog.com/about">About Us</a>
Nokogiri can also update and create HTML and XML.
	
	