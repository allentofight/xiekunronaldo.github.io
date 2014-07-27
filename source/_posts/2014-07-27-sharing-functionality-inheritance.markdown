---
layout: post
title: "Sharing Functionality: Inheritance"
date: 2014-07-27 15:03:31 +0800
comments: true
categories: 
---
####继承和消息

	class Parent 

		def say_hello	
		puts "Hello from #{self}" 
		end
	end
	p = Parent.new
	p.say_hello
		class Child < Parent # Subclass the parent...
	
	end	
	c = Child.new
	c.say_hello
	produces:
	Hello from #<Parent:0x007fb87110fd98>
	Hello from #<Child:0x007fb87110fac8>
superclass的方法返回了一个类的父类

	class Parent
	end
	class Child < Parent
	end
	Child.superclass # => Parent
那么，Parent的superclass是什么
	class Parent
	end
	Parent.superclass # => Object
继续探究
	
	Object.superclass # => BasicObject
	BasicObject.superclass.inspect # => "nil"
我们可以看到在继承体系中BasicObject是root class
####Modules
Modules提供了一种将方法，类和常量聚合在同一空间名下的方式,Modules提供了两大优势

1. Modules提供了一个命名空间，并且防止了命名冲突
2. Modules支持mixin的功能
	
		//trig.rb
		module Trig
			PI = 3.141592654 

			def Trig.sin(x)
			# ..
			end
			def Trig.cos(x) 

				# ..
			end 
		end
		//moral.rb
		module Moral
			VERY_BAD = 0
			BAD =1
			def Moral.sin(badness)
			# ...
			end 
		end
来看看如何使用它们

		require_relative 'trig' 
		require_relative 'moral'
		y = Trig.sin(Trig::PI/4)
		wrongdoing = Moral.sin(Moral::VERY_BAD)
####Mixins
Modules还有另外一个很好用的功能，它们可以在很大程序上避免继承的使用,这种方式叫mixin
从以上的module的demo中我们可以看到，如果我们定义modules的方法，这些方法前面加了module的名字,如果你想到了类方法，那么你可能会想"我是否能在module里定义instance方法"!好问题，一个module不能有instances，因为modules不是类，不过你可以将一个module添加到一个类的定义中，这样，所有的modules的instance方法就同类中的instance方法一样可用了，它们mixed in了，实际上 mixed-in 模块有效地充当了superclass的角色
来看下demo


		module Debug 

			def who_am_i?
				"#{self.class.name} (id: #{self.object_id}): #{self.name}" 			end
		end
		class Phonograph 
			include Debug 
			attr_reader :name 
			def initialize(name)

			@name = name
		end
		# ...		end
		class EightTrack 
		include Debug 
			attr_reader :name 
			def initialize(name)
			@name = name
		end
		# ...
		end
		ph = Phonograph.new("West End Blues")
		et = EightTrack.new("Surrealistic Pillow")
		ph.who_am_i? # => "Phonograph (id: 70266478767560): West End Blues" 
		et.who_am_i? # => "EightTrack (id: 70266478767520): Surrealistic Pillow"

mixin的真正威力在mixin的代码开始与使用它的类的代码进行交互时才得以体现出现,我们用Ruby的Mixin Comparable来作一个例子,Comparable mixin为类添加了比较方法(<, <=, ==, >=, 和 >)和between?方法，为了让Comparable工作,Comparable假设所有使用它的类定义了操作符`<=>`

	class Person
		include Comparable 
		attr_reader :name
			def initialize(name) 
				@name = name
			end
			def to_s
				"#{@name}" 
			end
			def <=>(other)
				self.name <=> other.name
			end 
		end
	p1 = Person.new("Matz") 
	p2 = Person.new("Guido")
	 	p3 = Person.new("Larry") # Compare a couple of names
	if p1 > p2
	puts "#{p1.name}'s name > #{p2.name}'s name"
	end
	# Sort an array of Person objects
	puts "Sorted list:" 

	puts [ p1, p2, p3].sort
produces:
	Matz's name > Guido's name
	Sorted list:
	Guido
	Larry
	Matz
####Iterators and the Enumerable Module
Ruby的collections类(Array, Hash等)支持许多针对collections的操作:遍历，排序等，你可能会想:"如果我的类也支持这些操作该多好"

好消息是，由于mixins和module Enumberable的存在,你的类能支持所有的这些特性，你需要做的只是写一个each的迭代器，依次返回你的collection里的元素，然后include Enumerable,这样你的类就支持map,include?,find_all?这些方法了，如果在你的collection里的对象用`<=>`来实现了一些有意义的排序相关的方法，你也能使用如min,max,sort等的方法
####Composing Modules
Enumerable是一个标准的mixin,实现了针对host class中的each相关的许多方法,其中一个是我们之前提到的inject方法，由于要用inject就得include Enumerable，我们能将它用在任何include Enumerable并且实现了each方法的类里
来看看许多内置类对它的使用

	[ 1, 2, 3, 4, 5 ].inject(:+) # => 15
	( 'a'..'m').inject(:+) # => "abcdefghijklm"
来看看如何定义我们自己的类来使用Enumerable中的inject
	
	//vowel_finder.rb
	class VowelFinder 
		include Enumerable
		def initialize(string)
		 @string = string
		end
		def each
			@string.scan(/[aeiou]/) do |vowel| 
			yield vowel
			end 

		end
	end	vf = VowelFinder.new("the quick brown fox jumped")	vf.inject(:+) # => "euiooue"
我们还能将inject的方法封装在module的方法中，如下

	module Summable 
		def sum			inject(:+)		end end	class Array 		include Summable	end	class Range 		include Summable	end
		require_relative "vowel_finder" 
	class VowelFinder
	  include Summable
	end
	[1,2,3,4,5].sum #=>15 

	('a'..'m').sum # => "abcdefghijklm"
	vf = VowelFinder.new("the quick brown fox jumped") 

	vf.sum # => "euiooue"#### Instance Variables in Mixins
从C++转过来的程序员通常会问"在mixin里的instance variable是如何处理的"
在Ruby中instace variables是这么工作的
以@开头的变量在当前的对象(self)里创建instace variable
对一个mixin而言,如果你在一个类中include了module,你的类对象将创建module里的instance variable并且可以用attr_reader和friends来定义accessors,比如,以下例子中的Observable module为每一个include它的类都添加了@observer_list的instance variable

	//observer_impl.rb

	module Observable 

		def observers

		    @observer_list ||= []
		end
		def add_observer(obj)
		    observers << obj
		end
		def notify_observers
		    observers.each {|o| o.update }
		end 

	end
然而这种方式可能会导致一些不可预知的错误,一个mixin的instance variables可能会与inclde它的host class定义的instance variables相冲突,以下的例子显示了一个使用我们的Observer module的类的例子，很不幸的是，它也定义了一个叫做@observer_list的instance variable,在运行时，这个程序可能会导致某些难以调试的错误

	require_relative 'observer_impl' 
	class TelescopeScheduler
		# other classes can register to get notifications # when the schedule changes
		include Observable
		def initialize
		@observer_list = [] # folks with telescope time
		end
		def add_viewer(viewer)
		    @observer_list << viewer
		end
		# ...
	end
所以，大部分情况下,mixin modules并不直接使用instance variables,它们用accessors来从client对象获取信息,不过如果你需要创建一个有它自己状态的mixin,你需要确保这些instance variables`必须`要有惟一的名字，或者也可以创建一个module-level的hash，(key用当前对象的ID表示)来存储instance-specific的data
	module Test
		State = {}
		def state=(value)
		    State[object_id] = value
		￼￼end

		def state State[object_id]
		end 
	end
	class Client 
		include Test
	end
	c1 = Client.new
	c2 = Client.new 
	c1.state = 'cat' 
	c2.state = 'dog' 
	c1.state # => "cat" 
	c2.state # => "dog"
使用这种方法的坏处就是，和某个特别的对象关联的数据在此对象被删后并不会马上释放，通常来说，一个需要维护它自己状态的mixin不应该是个mixin，它应该被写成一个类
####Resolving Ambiguous Method Names
如果多个被include的mixins，此类，此类的父类中有相同的方法该如何处理
Ruby会首选查看一个对象的类中的方法，然后是被include到mixins,然后是superclass以及其中的mixins,如果一个为类include了多个mixins,最后被include的mixin首先被搜索到
	
