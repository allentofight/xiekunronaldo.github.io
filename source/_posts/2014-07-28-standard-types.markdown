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
	       Fixnum: 10001	       Fixnum: 100020001	       Fixnum: 10004000600040001	       Bignum: 100080028005600700056002800080001
我们可以在数字前添加0x,0b等前缀,或是下划线

	123456 => 123456	# Fixnum	0d123456 => 123456		# Fixnum	123_456 => 123456		# Fixnum - underscore ignored	-543 => -543			# Fixnum - negative number	0xaabb => 43707			# Fixnum - hexadecimal	0377 => 255				# Fixnum - octal	-0b10_1010 => -42		# Fixnum - binary (negated)	123_456_789_123_456_789 => 123456789123456789 # Bignum
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
	34 56 78应该先把strings转为integers
	
	some_file.each do |line|
		v1, v2 = line.split
	print Integer(v1) + Integer(v2), " "
	end	produces:	7 11 15
#####How Number interact

	1 + 2					# => 3
	1 + 2.0 				# => 3.0
	1.0+2	 				# => 3.0
	
	1.0 + Complex(1,2) 		# => (2.0+2i)	1 + Rational(2,3) 		# => (5/3)	1.0 + Rational(2,3) 	# => 1.6666666666666665
如果你需要除法能返回 一个Rational number,则需引入mathn库,这个库能帮我们以最`自然`的方式展示两数运算的结果

	22 / 7 # => 3

	Complex::I * Complex::I # => (-1+0i)	require 'mathn'	22 / 7 # => (22/7) 
	Complex::I * Complex::I # => -1
#####Looping Using Numbers
	
	3.times { print "X " } 
	
	1.upto(5) {|i| print i, " " } 
	
	99.downto(95) {|i| print i, " " } 
	
	50.step(80, 5) {|i| print i, " " }
	
	produces:
		X X X 1 2 3 4 5 99 98 97 96 95 50 55 60 65 70 75 80
同其他迭代器一样，如果其后没有block，则会返回一个Enumerator对象
	
	10.downto(7).with_index {|num, index| puts "#{index}: #{num}"}	produces:       0: 10       1: 9       2: 8       3: 7
####Strings
	
	
