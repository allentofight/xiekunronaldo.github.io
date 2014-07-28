---
layout: post
title: "Standard Types"
date: 2014-07-28 08:10:44 +0800
comments: true
categories: Ruby
---
 这一章主要介绍Ruby中的几个标准类型:numbers,strings,ranges,regular expressions 
####Numbers
Integers可以是任何长度(内存有多大，长度就有多大),在一定范围内(通常是pow(-2,30)...pow(2,30)-1或是pow(-2,62)...pow(2,62)-1)的Integers是类Fixnum的对象,超出此范围的则是类Bignum的对象,这个转化过程由Ruby自动来处理

	num = 10001 4.times do
		puts "#{num.class}: #{num}" num *= num
	end
		produces:
	       Fixnum: 10001
	       Fixnum: 100020001
	       Fixnum: 10004000600040001
	       Bignum: 100080028005600700056002800080001
我们可以在数字前添加0x,0b等前缀,或是下划线

	123456 => 123456	# Fixnum
	0d123456 => 123456		# Fixnum
	123_456 => 123456		# Fixnum - underscore ignored
	-543 => -543			# Fixnum - negative number
	0xaabb => 43707			# Fixnum - hexadecimal
	0377 => 255				# Fixnum - octal
	-0b10_1010 => -42		# Fixnum - binary (negated)
	123_456_789_123_456_789 => 123456789123456789 # Bignum

Ruby支持rational和complex number,Rational numbers是两个整数的比	
	Rational(3, 4) * Rational(2, 3) # => (1/2) Rational("3/4") * Rational("2/3") # => (1/2)
	Complex(1, 2) * Complex(3, 4) # => (-5+10i) Complex("1+2i") * Complex("3+4i") # => (-5+10i)

需要注意的是,不像Perl，在表达式计算时，Strings中的数字并不会自动转为数字
假设有一个文字如下，我们需要计算每行两个数字的和

	3	4 
	
	5	6 
	
	7	8
以下的代码并不工作

	some_file.each do |line| 
	
	v1, v2 = line.split 		# split line on spaces
	
	print v1 + v2, " "
		end
	produces:
	34 56 78
应该先把strings转为integers
	
	some_file.each do |line|
		v1, v2 = line.split
	print Integer(v1) + Integer(v2), " "
	end
	produces:
	7 11 15
#####How Number interact

	1 + 2					# => 3
	1 + 2.0 				# => 3.0
	1.0+2	 				# => 3.0
	
	1.0 + Complex(1,2) 		# => (2.0+2i)
	1 + Rational(2,3) 		# => (5/3)
	1.0 + Rational(2,3) 	# => 1.6666666666666665
如果你需要除法能返回 一个Rational number,则需引入mathn库,这个库能帮我们以最`自然`的方式展示两数运算的结果

	22 / 7 # => 3

	Complex::I * Complex::I # => (-1+0i)
	require 'mathn'	22 / 7 # => (22/7) 
	Complex::I * Complex::I # => -1

#####Looping Using Numbers
	
	3.times { print "X " } 
	
	1.upto(5) {|i| print i, " " } 
	
	99.downto(95) {|i| print i, " " } 
	
	50.step(80, 5) {|i| print i, " " }
	
	produces:
		X X X 1 2 3 4 5 99 98 97 96 95 50 55 60 65 70 75 80
同其他迭代器一样，如果其后没有block，则会返回一个Enumerator对象
	
	10.downto(7).with_index {|num, index| puts "#{index}: #{num}"}
	produces:
       0: 10
       1: 9
       2: 8
       3: 7
####Strings
String有两种组成方式，单引号和双引号组成的string
单引号

	 'escape using "\\"'   # => escape using "\"
	      'That\'s right'       # => That's right
如果是双引号,你可以用#{expr}来替换strings里的值,如果expr是个全局变量，类变量，或是一个类的实例,花括号可以省略
	
	"Seconds/day: #{24*60*60}" 
	
	"#{'Ho! '*3}Merry Christmas!" "Safe level is #$SAFE"
		# => Seconds/day: 86400
	# => Ho! Ho! Ho! Merry Christmas!
		# => Safe level is 0
{}里的代码不一定只是一个表达式，也可以由多条语句构成

	puts "now is #{ def the(a)
					 'the ' + a
					end
					the('time')
					} for all bad coders..."
	produces:
	now is the time for all bad coders...
你还可以用另外三种方式来构建一个string常量:%q,%Q以及here documents
你可以把%q看成是单引号,%Q看成是双引号

	%q/general single-quoted string/		# => general single-quoted string
	
	%Q!general double-quoted string! 		# => general double-quoted string
	
	%Q{Seconds/day: #{24*60*60}}			# => Seconds/day: 86400
实际上Q是可有可无的

	%!general double-quoted string! # => general double-quoted string 
	
	%{Seconds/day: #{24*60*60}} # => Seconds/day: 86400

实际上紧跟在q或Q后的是分隔符,分隔符可以由任何`nonalphanumeric` 或 `nonmultibyte`字符组成
最后,我们来看看如何用`here document`来创建string

	string = <<END_OF_STRING
		The body of the string is the input lines up to		one starting with the same text that followed the '<<'	END_OF_STRING
通常terminator从第一行开始，不过，如果你把一个负号放在<<后，你可以对terminator进行缩进，创建的string也会进行缩进
	string = <<-END_OF_STRING
		The body of the string is the input lines up to		one starting with the same text that followed the '<<' 	END_OF_STRING
你也可以将多个here documents放在同一行上，每一个代表一个单独的string,here document的bodies从source lines中依次获取
	print <<-STRING1, <<-STRING2 
	Concat
	STRING1
	      enate
	      STRING2
	produces:
	Concat 
		enate
 注意在此情况下Ruby并不会将enate前的空格给去掉
 
####Strings and Encodings
每一个string都有一个对应的encoding,一个string的默认的encoding取决于包含它的源文件的encoding,如果没有指明显式的encoding,一个源文件(和它的string)在Ruby 1.9用的是US-ASCII编码，Ruby 2则是UTF-8

	plain_string = "dog"
		puts RUBY_VERSION
	puts "Encoding of #{plain_string.inspect} is #{plain_string.encoding}"

	produces:
	2.0.0
	Encoding of "dog" is UTF-8
如果你在文件的开头指明用了哪个encoding,文件中所有的string都 会用此encoding
	#encoding: utf-8
	plain_string = "dog"
	puts "Encoding of #{plain_string.inspect} is #{plain_string.encoding}" utf_string = "δog"
	puts "Encoding of #{utf_string.inspect} is #{utf_string.encoding}"
	produces:
	Encoding of "dog" is UTF-8
	Encoding of "δog" is UTF-8
######Working with Strings
假设一个文件有以下格式
	/jazz/j00132.mp3  | 3:45 | Fats     Waller     | Ain't Misbehavin'	/jazz/j00319.mp3  | 2:58 | Louis    Armstrong  | Wonderful World	/bgrass/bg0732.mp3| 4:09 | Strength in Numbers | Texas Red
现在我们要做以下三件事情

1. 将每一行分解成fields
2. 将时间从mm:ss转为秒
3. 移除artist中名字中的空格
先来看看第一步

		Song = Struct.new(:title, :name, :length)
		File.open("songdata") do |song_file| songs = []
		song_file.each do |line|
		file, length, name, title = line.chomp.split(/\s*\|\s*/) 
		name.squeeze!(" ")		songs << Song.new(title, name, length)
		end
		  puts songs[1]
		end
		produces:
		#<struct Song title="Wonderful World", name="Louis Armstrong", length="2:58">
注意上面的line.chomp,因为每一行都有一个换行符，所以必须把它去掉
来看下如何处理时间,用split也可以，但我们用更好的方式scan

		Song = Struct.new(:title, :name, :length) 
		
		File.open("songdata") do |song_file|
		songs = []
		song_file.each do |line|
		file, length, name, title = line.chomp.split(/\s*\|\s*/) 		name.squeeze!(" ")
		mins, secs = length.scan(/\d+/)
		songs << Song.new(title, name, mins.to_i*60 + secs.to_i)
		end
		puts songs[1]
		end
		produces:
		#<struct Song title="Wonderful World", name="Louis Armstrong", length=178>####Ranges
ranges主要用在以下三个方面:sequences, conditions和intervals

1.Ranges as Sequences
	1..10	'a'..'z' 0..."cat".length
你可以用to_a将一个range转为一个数组,用to_enum将它转为一个Enumberator

	(1..10).to_a # => [1, 2, 3, 4, 5, 6, 7, 8, 9, 10] 

	('bar'..'bat').to_a # => ["bar", "bas", "bat"]	enum = ('bar'..'bat').to_enum	enum.next # => "bar"	enum.next # => "bas"
Ranges有很多方法能让你遍历它们并且用很多方法测试它们
	digits = 0..9
	digits.include?(5) # => true
	digits.max # => 9
	digits.reject {|i| i < 5 } # => [5, 6, 7, 8, 9] 
digits.inject(:+) # => 45
目前我们已经看到了numbers和strings的ranges，实际上，ranges也可以基于你定义的对象来创建,惟一的要求是对象必须定义succ方法，这个方法返回了序列中的下一个对象，这个对象还必须实现`<=>`(有时称为`spaceship operator`),允许对象之间的比较,返回-1,0, 1
	class PowerOfTwo 
		attr_reader :value 
		def initialize(value)
    		@value = value
		end
		def <=>(other)
		    @value <=> other.value
		end
		def succ
		    PowerOfTwo.new(@value + @value)
		end
		def to_s
		@value.to_s
		end 

	end
	p1 = PowerOfTwo.new(4)
	p2 = PowerOfTwo.new(32)
	puts (p1..p2).to_a
	produces:
	4 
	8 
	16 
	32
#####Ranges as Conditions

	while line = gets
		puts line if line =~ /start/ .. line =~ /end/
	end
#####Ranges as Intervals
可以借此来查看某些值是否在range间,注意必须用`===`

	(1..10) === 5 # => true

	(1..10) === 15 # => false 

	(1..10) === 3.14159 # => true 

	('a'..'j') === 'c' # => true 

	('a'..'j') === 'z' # => false还可用在case中
	car_age = gets.to_f # let's assume it's 9.5 
	case car_age
	when 0...1
		puts "Mmm.. new car smell" 
	when 1...3
	puts "Nice and new" 
	when 3...10
		puts "Reliable but slightly dinged" 
	when 10...30
		puts "Clunker" 
	else
		puts "Vintage gem" 

	end
	produces:
	Reliable but slightly dinged
