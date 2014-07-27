---
layout: post
title: "容器-块-迭代器"
date: 2014-07-26 08:51:21 +0800
comments: true
categories: 
---
####数组
来看看数组是怎么用的

	a = [ 3.14159, "pie", 99 ] 
	a.class # => Array 
	a.length # => 3
	a[0] # => 3.14159
	a[1] # => "pie"
	a[2] #=>99
	a[3] #=>nil
	b = Array.new 
	b.class # => Array 

	b.length # => 0 
	b[0] = "second"
	b[1] = "array"
	￼b
	a = [ 1, 3, 5, 7, 9 ]
	a[-1] # => 9
	a[-2] # => 7
	a[-99] # => nil
	a = [ 1, 3, 5, 7, 9 ] 
	a[1,3] #=>[3,5,7] 
	a[3, 1] # => [7] 
	a[-3,2] #=>[5,7]
	a = [ 1, 3, 5, 7, 9 ] 
	a[1..3] #=>[3,5,7] 
	a[1...3] # => [3, 5] 
	a[3..3] #=>[7] 

	a[-3..-1] # => [5, 7, 9]
	
	a=[1,3,5,7,9] 		#=>[1,3,5,7,9]
	a[1] = 'bat'		#=> [1, "bat", 5, 7, 9]
	a[-3] = 'cat'		#=> [1, "bat", "cat", 7, 9]
	a[3] =[9,8]			#=> [1, "bat", "cat", [9, 8], 9]
	a[6] = 99			#=> [1, "bat", "cat", [9, 8], 9, nil, 99]

	a = [ 1, 3, 5, 7, 9 ]		#=>[1,3,5,7,9]
	a[2, 2] = 'cat'				#=> [1, 3, "cat", 9]
	a[2, 0] = 'dog' 			#=> [1, 3, "dog", "cat", 9] 
	a[1,1]=[9,8, 7] 			#=>[1,9,8,7,"dog","cat",9]
	a[0..3] = []				#=> ["dog", "cat", 9]
	a[5..6] = 99, 98			#=> ["dog", "cat", 9, nil, nil, 99, 98]
数组有许多有用的方法，使用它们，你能将数组作为栈(stacks),集和(sets),队列(queues),[dequeues](http://en.wikipedia.org/wiki/Double-ended_queue),先进先出的queues
比如,push和pop方法从数组的末端添加和删除元素，所以你能将数组作为栈来使用

	stack = []
	stack.push "red"
	stack.push "green"
	stack.push "blue"
	stack # => ["red", "green", "blue"]

	stack.pop # => "blue" 
	stack.pop # => "green" 
	stack.pop # => "red" 
	stack #=>[]

同样地,unshift和shift从数组的起始位置添加和删除元素,结合shift和push，你能构建一个FIFO的队列

	queue = []
	queue.push "red" 
	queue.push "green" 
	queue.shift # => "red" 
	queue.shift # => "green"
first和last方法返回一个数组的头部和尾部的n个元素
	array = [ 1, 2, 3, 4, 5, 6, 7 ] 
	array.first(4) # => [1, 2, 3, 4] 
	array.last(4) # => [4, 5, 6, 7]
####哈希(字典)
同数组一样,哈希也是针对数组引用的以索引查询的集合,不过与数组不同的是，数组的下标只能为整形，而哈希的key则可为symbols,strings,regular expression等
	h = { 'dog' => 'canine', 'cat' => 'feline', 'donkey' => 'asinine' }
	h.length # => 3
	h['dog'] # => "canine"
	h['cow'] = 'bovine'
	h[12] = 'dodecine'
	h['cat'] = 99
	h 			# => {"dog"=>"canine", "cat"=>99, "donkey"=>"asinine", "cow"=>"bovine",	            # .. 12=>"dodecine"}
从Ruby 1.9开始,如果key是symbols,你可以用以下方式创建hash
	h = { :dog => 'canine', :cat => 'feline', :donkey => 'asinine' }
也可以这么写

	h = { dog: 'canine', cat: 'feline', donkey: 'asinine' }####用哈希和数组来完成单词的计数
现在我们要完成一个demo,给定一段文字，从中计算出每个单词出现的频率
我们将问题分成两步

1. 将这一段文字作为一个字符串，然后返回此字符串里的单词(保存在数组中)
	
		def words_from_string(string) 
			string.downcase.scan(/[\w']+/)
		end
		p words_from_string("But I didn't inhale, he said (emphatically)")
		结果:
		["but", "i", "didn't", "inhale", "he", "said", "emphatically"]

2. 为每一个不同的单词都弄一个计数，这个就用hash来实现

			def count_frequency(word_list) 
			counts = Hash.new(0)
			for word in word_list
			    counts[word] += 1
			end
			counts
		end
		p count_frequency(["sparky", "the", "cat", "sat", "on", "the", "mat"]) 
		produces:
		{"sparky"=>1, "the"=>2, "cat"=>1, "sat"=>1, "on"=>1, "mat"=>1}
最后，计算好了hash后，我们要打印出来，这里我们打算按字数出现频率的升序顺序来打印

		sorted = counts.sort_by {|word, count| count}
综上，我们的程序如下

		require_relative "words_from_string.rb" 
		require_relative "count_frequency.rb"
		raw_text = %{The problem breaks down into two parts. First, given some text as a string, return a list of words. That sounds like an array. Then, build a count for each distinct word. That sounds like a use for a hash---we can index it with the word and use the corresponding entry to keep a count.}
		word_list = words_from_string(raw_text)
		counts    = count_frequency(word_list)
		sorted    = counts.sort_by {|word, count| count}
		top_five  = sorted.last(5)

`top_five  = sorted.last(5)`是一个数组,每一个元素对应着一个key/value的pair,来看看如何遍历它

	for i in 0...5 # (this is ugly code--read on 
		word = top_five[i][0] # for a better version) 
		count = top_five[i][1]
	puts "#{word}: #{count}"
	end
	produces:
	that:  2
	sounds:  2
	like:  2
	the:  3
	a: 6####单元测试
表面上我们对以上的方法进行单元测试
首先是对`words_from_string`

		require_relative 'words_from_string'
		require 'test/unit'
		class TestWordsFromString < Test::Unit::TestCase
			def test_empty_string
				assert_equal([], words_from_string("")) assert_equal([], words_from_string(" "))
			end
			def test_single_word
				assert_equal(["cat"], words_from_string("cat")) 
				assert_equal(["cat"], words_from_string(" cat "))
			end
			def test_many_words
				assert_equal(["the", "cat", "sat", "on", "the", "mat"],
				words_from_string("the cat sat on the mat")) 
			end

			def test_ignores_punctuation 
				assert_equal(["the", "cat's", "mat"],
				words_from_string("<the!> cat's, -mat-")) 

			end
		end
		produces:
		Run options:
		# Running tests:
		....
		Finished tests in 0.006458s, 619.3868 tests/s, 929.0802 assertions/s.		4 tests, 6 assertions, 0 failures, 0 errors, 0 skips

在测试框架中，所有以test开头的方法都会自动运行我们也能测试计算单词出现次数的方法
		require_relative 'count_frequency' 
		require 'test/unit'
		class TestCountFrequency < Test::Unit::TestCase 
			def test_empty_list
			    assert_equal({}, count_frequency([]))
			end

				def test_single_word
				assert_equal({"cat" => 1}, count_frequency(["cat"])) 
			end
			def test_two_different_words
				assert_equal({"cat" => 1, "sat" => 1}, count_frequency(["cat", "sat"]))
			end
			def test_two_words_with_adjacent_repeat
				assert_equal({"cat" => 2, "sat" => 1}, count_frequency(["cat", "cat", "sat"])) 
			end
			def test_two_words_with_non_adjacent_repeat
				assert_equal({"cat" => 2, "sat" => 1}, count_frequency(["cat", "sat", "cat"]))
			end 
		end
		produces:
		Run options:
		# Running tests:
		.....
		Finished tests in 0.006327s, 790.2639 tests/s, 790.2639 assertions/s.		5 tests, 5 assertions, 0 failures, 0 errors, 0 skips
####块和迭代器
还记得之前遍历top_five的语句吗,实际上，我们可以按如下方式来遍历数组

	top_five.each do |word, count| 
		puts "#{word}: #{count}"
	end
each方法就是一个迭代器-一个不断调用块的方法,实际上相较于以上的方法，很多程序员更愿意写成如下形式
	puts top_five.map { |word, count| "#{word}: #{count}" }
假设有以下程序

	square = Shape.new(sides: 4) # 假设 Shape 类在其他地方定义了 # .. lots of code
	sum = 0
	[1, 2, 3, 4].each do |value| 
		square = value * value
		sum += square
	end
	puts sum现在我们调用
	square.draw		//报错
因为在block里square的值被改变了

我们可以通过将仅作用域仅在块内部的变量放在块的参数列表后面(通过;来分隔)来避免此类现象的发生,如下


	[1, 2, 3, 4].each do |value; square| 
		square = value * value
		sum += square
	end
#####yield的使用
	
	def fib_up_to(max) 

		i1, i2 = 1, 1 

		while i1 <= max
		yield i1
		    i1, i2 = i2, i1+i2
		end 
	end	# parallel assignment (i1 = 1 and i2 = 1)

	fib_up_to(1000) {|f| print f, " " }
	puts
	produces:
	1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987
注意以下这行的使用
	print f, " "
一个块也能返回一个值，块最后一个表达式的值为yield执行后的值
这就是Array中的find方法工作的原理

	class Array 

		def find
			each do |value|
				return value if yield(value)
			end
		nil
		end 
	end	[1, 3, 5, 7, 9].find {|v| v*v > 30 } # => 7
迭代器collect的使用

	["H", "A", "L"].collect {|x| x.succ } # => ["I", "B", "M"]
迭代器并不限于访问array和hash中已存在的元素，如我们之前在Fibonacci中看到的,一个迭代器能返回推导后的值,

	
		yield i1
		    i1, i2 = i2, i1+i2
		end 
 同样的，这种特性也被Ruby用在输入输出类中,这些类返回了一个迭代器接口，这些接口在I/O流中返回了连续的行(或bytes),如下

	f = File.open("testfile") 

	f.each do |line|
		puts "The line is: #{line}" end
	f.close

	produces:
	The line is: This is line one
	The line is: This is line two
	The line is: This is line three
	The line is: And so on...
有时你想跟踪block被调用了多少次，这时你可以试试with_index方法,此方法一般是跟在一个迭代器(如each之后)
	
	f = File.open("testfile") 

	f.each.with_index do |line, index|
		puts "Line #{index} is: #{line}" 

	end
	f.close
	produces:
	Line 0 is: This is line one
	Line 1 is: This is line two

再看一个有用的迭代器

	[1,3,5,7].inject(0) {|sum, element| sum+element} # => 16 
	[1,3,5,7].inject(1) {|product, element| product*element} # => 105
等价于

	[1,3,5,7].inject {|sum, element| sum+element} # => 16 
	[1,3,5,7].inject {|product, element| product*element} # => 105
等价于

	[1,3,5,7].inject(:+) # => 16 
	[1,3,5,7].inject(:*) # => 105####Enumberators-外在迭代器
尽管以上的迭代器有许多好处,不过如果遇到以下情况，你会发现内部迭代器帮不上什么忙

1. 如果你要将迭代器当作一个对象时(比如代要将迭代器传给一个方法，这个方法需要访问这个迭代器里的每个元素)
2. 同时访问两个Collections的元素
幸运的是，Ruby中有一个内置的Enumerator类，实现了外在的迭代器，可以处理以上说的两种情况
你可以对array或hash来调用to_enum方法来创建一个Enumberator对象


		a = [ 1, 3, "cat" ]
		h = { dog: "canine", fox: "vulpine" }

		# Create Enumerators
		enum_a = a.to_enum
		enum_h = h.to_enum
		enum_a.next # => 1
		enum_h.next # => [:dog, "canine"] 

		enum_a.next # => 3
		enum_h.next # => [:fox, "vulpine"]

许多内在迭代器方法(产生连接的值传给block，如each) 如果不结合block使用的话将返回一个Enumerator对象

		
		a = [ 1, 3, "cat" ]
		enum_a = a.each # create an Enumerator using an internal iterator
		enum_a.next # => 1 enum_a.next # => 3
Ruby有一个叫做loop的方法，不做任何事情,主要工作就是不断地调用block,通常来说，如果某些条件发生了，block中的代码将跳出循环,但是当你用Enumerator时，loop可以足够聪明地分辨出一个enumerator对象是否遍历了所有的值，如果遍历完了，loop将终止
	short_enum = [1, 2, 3].to_enum 
	long_enum = ('a'..'z').to_enum
	loop do
		puts "#{short_enum.next} - #{long_enum.next}"
	end
	produces:
	1-a 2-b 3-c
####Enumerators是对象
Enumberators将某些可执行的代码(迭代的行为)转化成一个对象，这意味着你可以用enumerator来做一些普通的循环无法轻易做到的事情,比如Enumerable模块定义了一个each_with_index方法，这个方法触发了原先的each方法，同时也返回了一个index
	result = []
	[ 'a', 'b', 'c' ].each_with_index {|item, index| result << [item, index] } 	result # => [["a", 0], ["b", 1], ["c", 2]]

如果你想迭代并获取index但又不想用each而想用其他方法来控制迭代又该怎么做呢？比如你想循环string里的每个character,Enumerator派上用场了,如果你不给string的each_char方法一个block,它会返回一个enumerator,然后再调用enumetor的each_with_index方法

	result = []
	"cat".each_char.each_with_index {|item, index| result << [item, index] } 

	result # => [["c", 0], ["a", 1], ["t", 2]]

实际上这是一个很普遍的方法，所以Mats给我们提供了一个方法with_index

	
	result = []
	"cat".each_char.with_index {|item, index| result << [item, index] } 

	result # => [["c", 0], ["a", 1], ["t", 2]]


我们也可以显式地创建Enumerator对象，这里我们通过调用string的each_char方法来创建，然后我们对enumerator来调用to_s遍历它

	enum = "cat".enum_for(:each_char) enum.to_a # => ["c", "a", "t"]
如果我们用来构建enumerator的方法中有参数，我们可以将它们传入enum_for
	enum_in_threes = (1..10).enum_for(:each_slice, 3) 
	enum_in_threes.to_a # => [[1, 2, 3], [4, 5, 6], [7, 8, 9], [10]]
Enumerator对象也是可enumerable的(也就是说，适应于可enumerable对象的方法也是适用于enumerator的),比如我们可以用Enumerable的first方法


		triangular_numbers = Enumerator.new do |yielder| 


			number = 0
			count = 1
			loop do
			    number += count
			    count  += 1
			    yielder.yield number
			end 
		end
		p triangular_numbers.first(5)
		produces:
		[1, 3, 6, 10, 15]####Enumberators Are Generators and Filters
Generators

	triangular_numbers = Enumerator.new do |yielder| number = 0
		count = 1
				loop do
				    number += count
				    count += 1
				    yielder.yield number
				end

	end
	5.times { print triangular_numbers.next, " " } 

	puts
	produces:
	1 3 6 10 15
Filters

	triangular_numbers = Enumerator.new do |yielder| # ...
	# as before...
	# ...
	end
	def infinite_select(enum, &block) 

	Enumerator.new do |yielder|
	enum.each do |value|
	yielder.yield(value) if block.call(value)
	end

	end
	end
	p infinite_select(triangular_numbers) {|val| val % 10 == 0}.first(5)
	produces:
	[10, 120, 190, 210, 300]
等价于
	triangular_numbers = Enumerator.new do |yielder|
		 # ... as before
	end
	class Enumerator
		def infinite_select(&block)
			Enumerator.new do |yielder| 

				self.each do |value|
						yielder.yield(value) if block.call(value) 
				end
			end 

		end
	end
	p triangular_numbers
	   .infinite_select {|val| val % 10 == 0}
	   .infinite_select {|val| val.to_s =~ /3/ }
	   .first(5)
	produces:	[300, 630, 1830, 3160, 3240]####Blocks可以是对象
block被转化成了Proc类的对象并将它存储在类的属性中
	class ProcExample
	def pass_in_block(&action)
	    @stored_proc = action
	end
	def use_proc(parameter)
	    @stored_proc.call(parameter)
	end 

	end

	eg = ProcExample.new	eg.pass_in_block { |param| puts "The parameter is #{param}" } 
	eg.use_proc(99)
如果一个方法返回了一个block对象又如何调用?

	def create_block_object(&block) 

	block
	end

	bo = create_block_object { |param| puts "You called me with #{param}" } 
	bo.call 99	bo.call "cat"	produces:	You called me with 99	You called me with cat
实际上这种方式是很有用的，所以Ruby提供了两个方法将一个block转化为一个对象,lambda和Proc.new都接受了一个block来创建一个Proc类的对象

	bo = lambda { |param| puts "You called me with #{param}" } 

	bo.call 99	bo.call "cat"
	produces:
	You called me with 99
	You called me with cat

####Blocks Can Be Closures
	def n_times(thing) 

		lambda {|n| thing * n }

	end
	p1 = n_times(23)
	p1.call(3) # => 69
	p1.call(4) # => 92
	p2 = n_times("Hello ")
	p2.call(3) # => "Hello Hello Hello "
	Blocks and Iterators • 65
n_times方法返回了一个Proc对象并且引用了方法的参数，thing,尽管到block被调用时，参数已经超出范围了，参数仍然能被block访问，这叫做closure(上下文的参数在block或Proc的生命周期中仍能被访问)，两看另外一个例子
	def power_proc_generator 
		value = 1		lambda { value += value }	end	power_proc = power_proc_generator	puts power_proc.call	puts power_proc.call	puts power_proc.call	produces:	2 4 8
我们可以看到vaue值是一直变化的
####另一种创建Proc对象的方式
之前我们用以下方式来创建Proc
	
	lambda { |params| ... }
你也可以用以下方式来创建
	-> params { ... }


	proc1 = -> arg { puts "In proc1 with #{arg}" }
	proc2 = -> arg1, arg2 { puts "In proc2 with #{arg1} and #{arg2}" } 

	proc3 = ->(arg1, arg2) { puts "In proc3 with #{arg1} and #{arg2}" }
	proc1.call "ant" 

	proc2.call "bee", "cat" 

	proc3.call "dog", "elk"
		produces:
	In proc1 with ant
	In proc2 with bee and cat
	In proc3 with dog and elk

比起lambda,->的形式看紧凑，特别是当你要传多个Proc对象给一个方法的时候

	def my_if(condition, then_clause, else_clause) 

		if condition
		    then_clause.call
		else
		    else_clause.call
		end 
	end
	5.times do |val| 
		my_if val < 2,

			  -> { puts "#{val} is small" }, 

			  -> { puts "#{val} is big" }
	end
	produces:
	0 is small
	1 is small
	2 is big
	3 is big
	4 is big
####Block Parameter Lists
	proc1 = lambda do |a, *b, &block| 
		puts "a = #{a.inspect}"		
		puts "b = #{b.inspect}" 
		block.call	end	proc1.call(1, 2, 3, 4) { puts "in block1" }	produces:	a=1	b = [2, 3, 4] 

	in block1
另一种形式

	proc2 = -> a, *b, &block do 

		puts "a = #{a.inspect}" 

		puts "b = #{b.inspect}" 

		block.call
	end
	proc2.call(1, 2, 3, 4) { puts "in block2" }
	produces:
	a=1
	b = [2, 3, 4] in block2
	￼
	
		
	


	


	