---
layout: post
title: "More About Methods"
date: 2014-07-29 22:17:21 +0800
comments: true
categories: Ruby
---

#####定义一个方法

一个方法用关键字def来定义，方法名应该以一个小写字母或一个下划线开头，紧跟着字母，数字和下划线,方法名也许以一个?,!或=号结尾，一个返回bool值的方法通常以一个?结果

	1.even? # => false 
	
	2.even? # => true 

	1.instance_of?(Fixnum) # => true
	
危险的方法，或是修改调用者的方法，可以用一个!来结尾,有时这些方法叫做`bang methods`,比如，类String提供了chop和chop！两种方法，前者返回了一个修改的string，后者则修改了调用此方法的字符串
能出现在赋值运算符`=`左边的方法以一个`=`结尾,如

	class BookInStock
			attr_reader :isbn, :price
		def initialize(isbn, price) 
		@isbn = isbn
		@price = Float(price)
		end
		def price=(new_price) 
		@price = new_price
		end		# ...	end
	book = BookInStock.new("isbn1", 33.80) 
	book.price = book.price * 0.75
有参和无参方法定义如下

	def my_new_method(arg1, arg2, arg3) # 3 arguments 
	
		# Code for the method would go here	end
	def my_other_new_method # No arguments 
		# Code for the method would go here		end

可以为参数指定默认值

		def cool_dude(arg1="Miles", arg2="Coltrane", arg3="Roach") 
		"#{arg1}, #{arg2}, #{arg3}."
	end

	cool_dude							# => "Miles, Coltrane, Roach."
				cool_dude("Bart")					# => "Bart, Coltrane, Roach."
	cool_dude("Bart", "Elwood")			# => "Bart, Elwood, Roach."
	cool_dude("Bart", "Elwood", "Linus") # => "Bart, Coltrane, Linus."
后一个参数的默认值也可以指定为与前一个参数相关的表达式
	def surround(word, pad_width=word.length/2) 
		"[" * pad_width + word + "]" * pad_width
	end
	surround("elephant") # => "[[[[elephant]]]]" 
	surround("fox") # => "[fox]"
	surround("fox", 10) # => "[[[[[[[[[[fox]]]]]]]]]]"#####可变参数
	def varargs(arg1, *rest)
		"arg1=#{arg1}. rest=#{rest.inspect}"
	end
	varargs("one")					# => arg1=one.  rest=[]
	varargs("one", "two")			# => arg1=one.  rest=["two"]
	varargs "one", "two", "three"	# => arg1=one.  rest=["two", "three"]	
有时我们用*来指定不被此类方法但被superclass中的此同名方法调用的参数,注意这种情况下我们直接调用super(无任何参数)，这种特殊的情况下意味着"在superclass中调用此方法，此所有的参数传到此类super的同名方法"

	class Child < Parent
	def do_something(*not_used)
	    # our processing
	super end
	end

你也可以只写一个*

	class Child < Parent 
	
		def do_something(*)
			    # our processing
				super 
			end
	end

你可以将带*的参数放在方法参数列表的任意位置

	def split_apart(first, *splat, last)
			puts "First: #{first.inspect}, splat: #{splat.inspect}, " +		"last: #{last.inspect}" 
	end
	split_apart(1,2)
	split_apart(1,2,3)
	split_apart(1,2,3,4)
	produces:
	First: 1, splat: [], last: 2
	First: 1, splat: [2], last: 3
	First: 1, splat: [2, 3], last: 4
	你也可以只关心起始的参数，这样，你可以像以下这样定义方法
	def split_apart(first, *, last)
#####Methods and Blocks
方法可以用yield来调用与它相关联的block
	def double(p1) yield(p1*2)
	end
	double(3) {|val| "I got #{val}" } # => "I got 6" 
	double("tom") {|val| "Then I got #{val}" } # => "Then I got tomtom"如果一个方法的最后一个参数以&开头，任何相关的block都能当作参数传入方法并被转化为一个Proc对象
	class TaxCalculator
		def initialize(name, &block)
				@name, @block = name, block
			end
			def get_tax(amount)
				"#@name on #{amount} = #{ @block.call(amount) }" 
			end
	end
	tc = TaxCalculator.new("Sales tax") {|amt| amt * 0.075 } 
	tc.get_tax(100) # => "Sales tax on 100 = 7.5"
	tc.get_tax(250) # => "Sales tax on 250 = 18.75"

#####调用方法

	connection.download_mp3("jitterbug") {|p| show_progress(p) } //对象
	
	File.size("testfile") # => 66 			//类
	
	Math.sin(Math::PI/4) # => 0.7071067811865475    //Module
	
如果省略了receiver,默认是self

	class InvoiceWriter
			def initialize(order)

		    @order = order
		    		end
		def write_on(output)
		    write_header_on(output)
		    write_body_on(output)
		    write_totals_on(output)
		end

	private		def write_header_on(output)
		# ...
		end
		def write_body_on(output)
		# ...
		end
		def write_totals_on(output)
		# ...
		end 
	end#####将参数传给方法
在不引起歧义的情况下，你可以省略括号，不过通常不推荐这么做
	# for some suitable value in 
	obj: a = obj.hash # Same as
	a = obj.hash() # this.
	obj.some_method "Arg1", arg2, arg3 # Same thing as //1.8之前
	obj.some_method("Arg1", arg2, arg3) # with parentheses.	如果你将多个参数传给return语句，方法将会以数组的形式返回这些参数
	def meth_three 100.times do |num|
	   square = num*num
	return num, square if square > 1000 end
	end
	meth_three # => [32, 1024]
你可以按以下方式来获取返回的值
	num, square = meth_three 
	num # => 32
	square # => 1024	
#####*!在方法调用中展开Collections
	def five(a, b, c, d, e)
		"I was passed #{a} #{b} #{c} #{d} #{e}"
	end
	five(1,2,3,4,5) #=>"Iwaspassed12345" 
	five(1, 2, 3, *['a', 'b']) # => "I was passed 1 2 3 a b" 
	five(*['a', 'b'], 1, 2, 3) # => "I was passed a b 1 2 3" five(*(10..14)) 
	#=>"Iwaspassed1011121314" 
	five(*[1,2], 3, *(4..5)) #=>"Iwaspassed12345"
Ruby 1.9中， 带*参数可以出现参数列表中的任意位置

#####Block的妙用

先来看一段代码

	print "(t)imes or (p)lus: " operator = gets
	print "number: "
	number = Integer(gets)
	if operator =~ /^t/
	puts((1..10).collect {|n| n*number }.join(", "))
	else
	puts((1..10).collect {|n| n+number }.join(", ")) 
	end
	produces:
	(t)imes or (p)lus: t
	number: 2
	2, 4, 6, 8, 10, 12, 14, 16, 18, 20
以上的代码能工作，但是很丑陋，我们注意到if和else的语句几乎一样，可以用block来改进如下
	print "(t)imes or (p)lus: " 
	operator = gets
	print "number: "
	number = Integer(gets)
	if operator =~ /^t/
	calc = lambda {|n| n*number }
	else
	  calc = lambda {|n| n+number }
	end
	puts((1..10).collect(&calc).join(", "))
	produces:
	(t)imes or (p)lus: t
	number: 2
	2, 4, 6, 8, 10, 12, 14, 16, 18, 20
如果方法的最后一个参数是以&开头的，则Ruby认为它是一个Proc对象，方法将它移除出参数列表，然后将Proc对象转为block，再与方法关联
#####Hash and Keyword Arguments
先来看一段代码

	class SongList
		def search(field, params)
			# ...
			end 
	end
	list = SongList.new
	list.search(:titles, { genre: "jazz", duration_less_than: 270 })
实际上这种方式不值得推荐，{}很容易让人误认为是一个block，所以Ruby提供了一个捷径，在参数列表中你可以传入key => value这种形式,只要它们跟在正常的参数之后，在任何的*和block参数之前，所有的这些键值对都将被组装成一个hash参数传入方法中,可以略去{}	
	list.search(:titles, genre: "jazz", duration_less_than: 270)

#####Keyword Argument Lists
我们仔细研究一下search方法，它接受一个field的名字和一个options hash,也许我们想要默认的duration为120s，并且加入验证方法-非法的验证不让它通过

Ruby 2.0之前，代码是这么写的

	def search(field, options)
		options = { duration: 120 }.merge(options) 
		if options.has_key?(:duration)
		    duration = options[:duration]
		    options.delete(:duration)
		end
		if options.has_key?(:genre)
		    genre = options[:genre]
		    options.delete(:genre)
		end
		fail "Invalid options: #{options.keys.join(', ')}" unless options.empty? 
		# rest of method
	end	如果是Ruby 2.0，可以简化为以下
	def search(field, genre: nil, duration: 120) 
		p [field, genre, duration ]
	end	search(:title)
	search(:title, duration: 432)
	search(:title, duration: 432, genre: "jazz")
	produces:
	[:title, nil, 120]
	[:title, nil, 432]
	[:title, "jazz", 432]
如果传入的是一个非法选项，Ruby将会报错
	search(:title, duraton: 432)
	produces:
	//是duration，不是duraton!	prog.rb:5:in `<main>': unknown keyword: duraton (ArgumentError)
你也可以将多个hash参数整合为一个hash参数，只要在方法的参数列表里在某参数的前面加`两个`星号
	def search(field, genre: nil, duration: 120, **rest) 
		p [field, genre, duration, rest ]
	end
	search(:title, duration: 432, stars: 3, genre: "jazz", tempo: "slow") 
	produces:
	[:title, "jazz", 432, {:stars=>3, :tempo=>"slow"}]
