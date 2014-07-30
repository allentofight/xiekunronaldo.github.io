---
layout: post
title: "类-对象-变量"
date: 2014-07-23 21:41:14 +0800
comments: true
categories: Ruby
---
假设有以下文件

	
			//data.csv
			
			"Date","ISBN","Price"
			"2013-04-12","978-1-9343561-0-4",39.45
			"2013-04-13","978-1-9343561-6-6",45.67
			"2013-04-14","978-1-9343560-7-4",36.95
现在我们的任务就是从这个CSV文件中计算出有相同title的书各有多少本，以及书的总价
我们设计了一个BookInStock的类

	class BookInStock
	 
	end
用new来创建对象

	a_book = BookInStock.new
以此创建的类无任何信息，我们可以为此类提供一个initialize的方法，此方法能让我们在每个对象都创建好后就设置对象的状态，我们将此状态存储在实例变量中(以@开头),现在我们更新如下

	class BookInStock
		def initialize(isbn, price)
			@isbn = isbn
		    @price = Float(price)
		end 
	end
initialize是一个特别的方法，当你调用BookInStore.new去创建一个对象的时候,Ruby分配了一些内存给此未初始化的对象，然后调用了initialize的方法，传递了任何传递给new方法的参数，这样就给了你一次设置对象状态的机会

		b1 = BookInStock.new("isbn1", 3) 
	p b1

	produces:
		#<BookInStock:0x007fac4910f3e0 @isbn="isbn1", @price=3.0>

p方法能打印出一个对象的内部表示形式(如@isbn, @price)而puts方法只是简单地将字符串输出到标准输出上，当你将一个对象传给puts时，由于puts不知道如何处理，所以它作了一个简单地处理:对象类的名字+:+对象的内存地址,我们可以改变puts的这种输出形式，只要重写to_s即可

	class BookInStock
		def initialize(isbn, price)
				@isbn = isbn
		        @price = Float(price)
		end
			def to_s
		"ISBN: #{@isbn}, price: #{@price}" 
		end
	end
			b1 = BookInStock.new("isbn1", 3)
	puts b1

结果	ISBN: isbn1, price: 3.0
#####对象和属性
以上的@isbn, @price不能访问，无任何意义，可以为它添加get方法
	
	class BookInStock
		def initialize(isbn, price)
			@isbn = isbn
		    @price = Float(price)
		end
		def isbn
			@isbn
		end

		def price 
			@price
		end
	# ..
	end
	book = BookInStock.new("isbn1", 12.34) 

	puts "ISBN = #{book.isbn}"
	puts "Price = #{book.price}"
结果
		ISBN   = isbn1
	Price  = 12.34
由于get方法的使用是如此频繁,Ruby提供了一个简便的方法，attr_reader能为你创建这些属性的get方法

	class BookInStock	
	  attr_reader :isbn, :price
		def initialize(isbn, price) 
			@isbn = isbn
			@price = Float(price)
		end		# ..	end	book = BookInStock.new("isbn1", 12.34) 

	puts "ISBN = #{book.isbn}"
		puts "Price = #{book.price}"
结果:	ISBN   = isbn1	Price  = 12.34对应,attr_reader后面跟的是symbols
这里通常有一个误解，特别是那些学过Java和C#的人,他们认为attr_reader在某种程序上声明了实例变量，实际上是没有的，它只是生成了访问方法，但无声明任何变量，这些变量只有在你使用它们的时候才出现，Ruby是完全让实例变量与访问方法解藕的
####可写属性
以上我们只是设置了只读方法,现在我看看如何设置可写方法

	class BookInStock
		attr_writer :isbn, :price
		def initialize(isbn, price) 

			@isbn = isbn
			@price = Float(price)
		end
		def price=(new_price) 
			@price = new_price
		end
	# ...
	end
	
	book = BookInStock.new("isbn1", 33.80) 
	puts "ISBN = #{book.isbn}"
	puts "Price = #{book.price}" 
	book.price = book.price * 0.75
	puts "New price = #{book.price}"
结果:
	ISBN      = isbn1
	Price     = 33.8
	New price = 25.349999999999998

同样的,Ruby也提供了一个创建属性设置方法的捷径，如果你想有一个只写不可读的方法，可以使用attr_writer,不过这种情况极其罕见，如果你想为某个属性设置可读可写的方法，你可以用attr_accessor

		class BookInStock 
		attr_reader :isbn 
		attr_accessor :price
		def initialize(isbn, price) 
			@isbn = isbn
			@price = Float(price)
		end
	# ...	end
	book = BookInStock.new("isbn1", 33.80) 
	puts "ISBN = #{book.isbn}"
	puts "Price = #{book.price}" 
	book.price = book.price * 0.75
	puts "New price = #{book.price}"
	结果:	ISBN      = isbn1
	Price     = 33.8
	New price = 25.349999999999998
####虚属性

	class BookInStock 

		attr_reader :isbn
		attr_accessor :price
		def initialize(isbn, price) 

			@isbn = isbn
			@price = Float(price)
		end
		def price_in_cents 
			Integer(price*100 + 0.5)
		end
	# ...
	end
我们看看以下会输出什么
	puts "Price in cents = #{book.price_in_cents}"
结果

	rice in cents = 3380

`book.price_in_cents`表示的就是虚属性，由于`price_in_cents`不对应任何对象的属性，但我们却提供了访问方法，依然可以通过.的形式访问它,同样的，我们也可以提供一个它的写方法

	def price_in_cents=(cents) 

		@price = cents / 100.0	end

	book = BookInStock.new("isbn1", 33.80)
	puts "Price = #{book.price}"
	puts "Price in cents = #{book.price_in_cents}" 


	book.price_in_cents = 1234
	puts "Price = #{book.price}"
	puts "Price in cents = #{book.price_in_cents}"
	produces:
	Price          = 33.8
	Price in cents = 3380
	Price          = 12.34
	Price in cents = 1234

####类与其他类的协同工作
已知BookInStock定义如上,现在不少csv文件，我们需要将csv文件的每一行转为一个BookInStock,并将每一个BookInStock保存到一个数组中，然后计算所有书的总价,所计算的几个csv文件我们通过命令行传入,现在我们将这个程序分成三个rb文件来操作(假设这三个文件在同一目录下)

1. book_in_stock.rb

		class BookInStock 

			attr_reader :isbn, :price
			def initialize(isbn, price) 
				@isbn = isbn
				@price = Float(price)
			end 
		end


2.csv_reader.rb
此文件有两个外部信赖，它需要标准的CSV的库，也需要存储在book_in_stock.rb中的BookInStock的类,我们用require来加载Ruby的CSV库，用require_relative来加载book_in_stock文件

	require 'csv'
	require_relative 'book_in_stock'

	class CsvReader 

		def initialize
		    @books_in_stock = []
		end
		def read_in_csv_data(csv_file_name) 
			CSV.foreach(csv_file_name, headers: true) do |row|
				@books_in_stock << BookInStock.new(row["ISBN"], row["Price"]) 
			end
		end 
	end
3.stock_stats.rb
这是我们的主程序
	
	require_relative 'csv_reader'	reader = CsvReader.new
	ARGV.each do |csv_file_name|		STDERR.puts "Processing #{csv_file_name}" 
		reader.read_in_csv_data(csv_file_name)	end
	￼￼￼puts "Total value = #{reader.total_value_in_stock}"

运行程序如下

	$ ruby stock_stats.rb data.csv
	Processing data.csv
	Total value = 122.07000000000001

#####访问控制
Ruby给你三种访问控制权限

* Public
* Protected
* Private
三种访问控制权限与其他语言的一样，不过有一点与其他面向对象的语言很不一样，那就是，Ruby中的访问控制是程序运行后才被确定的，是动态的，而不是静态的，只有当代码执行了访问受限的代码才能知道是否有访问不合法的错误发生
######如何指定访问控制
有以下两种方法

		class MyClass

			def method1		# 默认是 'public'

				#...

			end

		protected

			def method2

				#....

			end

		private 

			def method3

				#...

			end

		end

或

			class MyClass 

				def method1 

				end				def method2 				end
		# ... and so on			  public    :method1, :method4			  protected :method2			  private   :method3		end
####变量 

		person = "Tim"
		puts "The object in 'person' is a #{person.class}" 
		puts "The object has an id of #{person.object_id}" 
		puts "and a value of '#{person}'"
		produces:
	       The object in 'person' is a String
	       The object has an id of 70230663692980
	       and a value of 'Tim'

那么，变量是否是一个对象呢，答案是否定的，一个变量只是一个对象的引用,来看以下例子，一目了然

		person1 = "Tim"

		person2 = person1 

		person1[0] = 'J'
		puts "person1 is #{person1}" 
		puts "person2 is #{person2}"		produces:		  		       person1 is Jim		       person2 is Jim
		       
你可以用String的
dup方法来创建一个全新的对象
	
		person1 = "Tim"		person2 = person1.dup person1[0] = "J"		puts "person1 is #{person1}" puts "person2 is #{person2}"		produces:		person1 is Jim		person2 is Tim我们也可以冻结一个对象来避免对它作出修改
		person1 = "Tim"		person2 = person1		person1.freeze # prevent modifications to the object person2[0] = "J"		produces:		        from prog.rb:4:in `<main>'		prog.rb:4:in `[]=': can't modify frozen String (RuntimeError)