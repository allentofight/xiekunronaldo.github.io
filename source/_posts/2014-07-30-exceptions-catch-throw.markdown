---
layout: post
title: "Exceptions-catch-throw"
date: 2014-07-30 11:52:27 +0800
comments: true
categories: Ruby
---
####The Exception Class
异常的信息被封装进了Exception类的对象或者Exception类的子类对象里,Ruby预定义了exceptions的hierarchy,如下

{% img /images/2014/7/exception_hierarchy.png%}

如果你需要抛出异常,你可以用内置的Exception的类,或者可以自己创建一个，不过请确保你创建的Exception
子类继承自StandardError或者它的子类,否则默认情况下异常是不会被捕捉的
每一个Exception都有一个消息和stack backtrace与之关联，如果你定义了自己的异常，你可以添加额外的信息
这是有一个用open-url的库来下载一个网页的内容并将它写入一个文件中的demo

	require 'open-uri'
	web_page = open("http://pragprog.com/podcasts") 
	output = File.open("podcasts.html", "w")
	while line = web_page.gets

	output.puts line
	end
	output.close

如果中途出现错误怎么办，我们当然不想保存一张不完整的网页
我们看看如何处理异常的代码，我们将可能发生异常的代码放在begin/end的block中，然后用rescue语句来告诉Ruby我们想要捕获的异常类型

	require 'open-uri'
	page = "podcasts"
	file_name = "#{page}.html"
	web_page = open("http://pragprog.com/#{page}") output = File.open(file_name, "w")
	begin
		while line = web_page.gets output.puts line
	end
	output.close 
	rescue Exception
		STDERR.puts "Failed to download #{page}: #{$!}" output.close
			File.delete(file_name)
			raise
	end	
When an exception is raised and independent of any subsequent exception handling, Ruby places a reference to the associated exception object into the global variable $! (the exclamation point presumably mirroring our surprise that any of our code could cause errors). In the previous example, we used the $! variable to format our error message.
关掉并删除文件后，我们可以调用无参的raise，这样又在$!里重新抛出了异常，抛到更高层去了
还可以为捕获取的异常指定别名

	begin
		eval string
	rescue SyntaxError, NameError => boom
		print "String doesn't compile: " + boom 
	rescue StandardError => bang
		print "Error running script: " + bang 
	end
#####System ErrorsSystem errors are raised when a call to the operating system returns an error code,On POSIX systems, these errors have names such as EAGAIN and EPERM. (If you’re on a Unix box, you could type man errno to get a list of these errors.)

Ruby将这些错误打包成一个具体的exception的对象,每一个是SymstemCallError的子类，并且都定义在Errno module中,这意味着你将看到Excetion的类另如Errno::EAGAIN, Errno::EIO, 和Errno::EPERM,如果你想要获取system error code,每个Error exception类都有一个叫做Errno类常量

	Errno::EAGAIN::Errno # => 35 
	
	Errno::EPERM::Errno # => 1 

	Errno::EWOULDBLOCK::Errno # => 35
注意EWOULDBLOCK 和 EAGAIN有同样的error code,所以在rescue中，Ruby把它们看作一样的，如果你想rescue一个，你再rescue两个，为此你可以重新定义 SystemCallError#===,这样，如果两个SystemCallError比较就会基于error number而不是它们在层次中的位置了

ensure相当于java中的finally
	
	f = File.open("testfile") 

	begin
	# .. process
	rescue
	  # .. handle error
	ensure
	f.close
	end
注意File.open不能放在begin里面，因为open它自己也可能抛出异常，如果发生了，你应该不想在ensure里来执行相应的代码，因为没有文件要去close

else语句

	f = File.open("testfile") 

	begin
	# .. process
	rescue
	  # .. handle error
	else
	puts "Congratulations-- no errors!" 
	ensure
	f.close
	end
注意else里的语句只有在无异发生时才会调用
有时你能改正抛出异常的cause,此时可以用retry语句来重新执行begin/end的block

	@esmtp = true
	begin
		# First try an extended login. If it fails, fall back to a normal login
		if @esmtp then @command.ehlo(helodom) 
						else @command.helo(helodom)
			end
	rescue ProtocolError
		if @esmtp then
			    @esmtp = false
				retry 
			else
				raise
			end 
	end
#####Raising Exceptions
你可以用Object#raise方法来抛出异常
	raise	//抛出当前的exception,如果没有，则抛出RuntimeError
	raise "bad mp3 encoding"	//创建一个新的RuntimeError,然后抛到调用栈上

	//创建InterfaceException，与之相关的信息，然后抛到stack trace上	raise InterfaceException, "Keyboard failure", caller
stack trace通常用Object#caller
也可以将Exception抛给call stack一部分的子集
假设我们定义了一个RetryException类
	class RetryException < RuntimeError
		attr :ok_to_retry
			def initialize(ok_to_retry)
			           @ok_to_retry = ok_to_retry
			end 
	end

在代码的某个地方，一个错误发生了

	def read_data(socket) 

		data = socket.read(512) 

		if data.nil?
		raise RetryException.new(true), "transient read error" 
		end        # .. normal processing	endhigher up te call stack,我们处理异常
	begin        stuff = read_data(socket)        # .. process stuff	rescue RetryException => detail 
		retry if detail.ok_to_retry 
		raise	end
#####catch and throw
	word_list = File.open("wordlist") 
	word_in_error = catch(:done) do
			result = []
			while line = word_list.gets
		word = line.chomp
		throw(:done, word) unless word =~ /^\w+$/ 
			result << word
		end
		  puts result.reverse
	end
	if word_in_error
		puts "Failed: '#{word_in_error}' found, but a word was expected" 
	end
	
throw不一定非要出现在catch语句里

	def prompt_and_get(prompt)
		print prompt
		res = readline.chomp
		throw :quit_requested if res == "!" 
		res
	end
	catch :quit_requested do
		name = prompt_and_get("Name: ") 
		age = prompt_and_get("Age: ") 
		sex = prompt_and_get("Sex: ") 
		# ..
		# process information
	end	