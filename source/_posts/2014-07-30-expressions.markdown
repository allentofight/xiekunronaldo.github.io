---
layout: post
title: "Expressions"
date: 2014-07-30 09:14:04 +0800
comments: true
categories: Ruby
---

显而易见的是我们可以chain statements

	a = b = c = 0 # => 0
	[ 3, 1, 7, 0 ].sort.reverse # => [7, 3, 1, 0]
比如不明显的是，在C或Java中的语句在Ruby中通常是expressions,比如if和case语句都返回了最后一个表达式
	song_type = if song.mp3_type == MP3::Jazz
					if song.written < Date.new(1935, 1, 1)
						Song::TradJazz
					else
						Song::Jazz
					end 
				else
					Song::Other
				end
	rating = case votes_cast
	￼			when 0...10		then Rating::SkipThisOne
				when 10...50 	then Rating::CouldDoBetter 
				else	Rating::Rave
			 end
#####Operator Expressions
在Ruby中，许多operations是以方法调用的形式实现的，比如，当你写a*b+c时，实际上你是调用了a的方法`*`传入参数b,然后a`*`b的结果返回一个对象，我们再对其执行`+`的方法,然后将c作为参数传入,实际下相当于以下代码

	a, b, c = 1, 2, 3
	a * b + c # => 5
	 (a.*(b)).+(c) # => 5
我们问题可以定义最基本的运算符操作
	class Fixnum
	alias old_plus + # We can reference the original '+' as 'old_plus'
	def +(other) # Redefine addition of Fixnums. This is a BAD IDEA! 
	old_plus(other).succ
	end end
	1 + 2 # => 4 a=3
	a+=4 #=>8 
	a + a + a # => 26	 

######<<操作符
	
	a = [ 1, 2, 3 ]
		a<<4 #=>[1,2,3,4]
我们也可以定义<<操作符方法
	class ScoreKeeper 
		def initialize
		           @total_score = @count = 0
			end
			def <<(score)
			           @total_score += score
			           @count += 1
			           self
			end
			def average
				fail "No scores" if @count.zero?
			    Float(@total_score) / @count
			end 
	end	     
	scores = ScoreKeeper.new
	scores << 10 << 20 << 40
	puts "Average = #{scores.average}"
	produces:
	       Average = 23.333333333333332
注意<<方法中返回了self，这样它就允许`scores << 10 << 20 << 40`这样的形式
`[]`也能被定义为一个方法调用，你可以这么写

	some_obj[1,2,3]
我们可以这样定义
	class SomeClass
		def [](p1, p2, p3)
			# ...
			end
	end

同样地，也可以定义`[]=`方法	class SomeClass 
		def []=(*params)
			value = params.pop
			puts "Indexed with #{params.join(', ')}" 
			puts "value = #{value.inspect}"
		end
	end
	s = SomeClass.new
	s[1] = 2
	s['cat', 'dog'] = 'enemies'
	produces:
	       Indexed with 1
	       value = 2
	       Indexed with cat, dog
	       value = "enemies"
从上面我们可以看到`[]`里的参数作为params的前n个数，而=右边的值为params的最后一个参数
#####各种各样的表达式
* Command Expression
以\`\`括起来的字符串或以%x开头的字符串被OS解析为一条bash命令,注意换行符还在
		`date` # => "Mon May 27 12:30:56 CDT 2013\n" 
		`ls`.split[34] # => "newfile"
			%x{echo "hello there"} # => "hello there\n"
				for i in 0..3
			status = `dbmanager status id=#{i}` # ...
		end
我们可以通过全局变量$?来查看命令的exit status
####重定义`
以上我们得知\`\`里的字符串默认被当作命令来执行，实际上，string是被当作参数传给了方法Object#\`,如果你想，可以重写它，
	alias old_backquote ` 
		def `(cmd)
			result = old_backquote(cmd) 
			if $? != 0
				puts "*** Command #{cmd} failed: status = #{$?.exitstatus}" 
			end		result	end
    print `ls -l /etc/passwd`
    print `ls -l /etc/wibble`
    produces:
       -rw-r--r--  1 root  wheel  5086 Jul 20  2011 /etc/passwd
       ls: /etc/wibble: No such file or directory
       *** Command ls -l /etc/wibble failed: status = 1
####赋值
Ruby有两种形式的赋值，第一种是将对象的引用赋予一个变量或常量
		instrument = "piano" 
	MIDDLE_A = 440
 第二种形式涉及到`having an object attribute or element reference on the left side.`这种形式是很特别的，因为它们通过在左值中调用方法来实现，也就是说你可以重写这些方法
	 class ProjectList 
		 def initialize
		    @projects = []
		end
		def projects=(list)
			@projects = list.map(&:upcase) # store list of names in uppercase 
		end
		def [](offset) 
			@projects[offset]
		end 
	end
	list = ProjectList.new
	list.projects = %w{ strip sand prime sand paint sand paint rub paint }
	list[3] # => "SAND"
	list[4] # => "PAINT"
来看一段有意思的代码

	class Test
		def val=(val)
			@val = val
			return 99 
		end
	end

	t = Test.new
		result = (t.val = 2)
	result
以上的result会是什么值?
在Ruby的过去版本中，赋值方法的返回值`总量`return的值，而从Ruby1.8开始,返回值总是传入的参数值
所以以上的result值如果在过去的版本中为99，但在1.8版本后则为2
	
######Parallel Assignment
	
	a,b=1,2 #a=1,b=2 

	a,b=b,a #b=2,a=1
	a=1,2,3,4 # a=[1,2,3,4] 
		b=[1,2,3,4] # b=[1,2,3,4]	
	a,b=1,2,3,4 # a=1, b=2 
	c,=1,2,3,4 # c=1

#####Splats and Assignment
	
	a, b, c, d, e = *(1..2), 3, *[4, 5] # a=1, b=2, c=3, d=4, e=5 		a,*b=1,2,3 # a=1, b=[2,3] a,*b=1 # a=1, b=[]
	*a,b=1,2,3,4 		# a=[1,2,3], b=4
	c,*d,e=1,2,3,4 		# c=1, d=[2,3], e=4
	f,*g,h,i,j=1,2,3,4	# f=1, g=[], h=2, i=3, j=4		first, *, last = 1,2,3,4,5,6 # first=1, last=6#####嵌套赋值
	a, (b, c), d = 1,2,3,4			# a=1, b=2, c=nil, d=3
	a, (b, c), d = [1,2,3,4] 		# a=1, b=2, c=nil, d=3
	a, (b, c), d = 1,[2,3],4 		# a=1, b=2, c=3, d=4
	a, (b, c), d = 1,[2,3,4],5 		# a=1, b=2, c=3, d=5
	a, (b,*c), d = 1,[2,3,4],5		# a=1, b=2, c=[3, 4], d=5Ruby中木有++和--运算符
####Conditional Execution
	var ||= "default value"
相当于
	var = "default value" unless var 
#####defined?
	defined? 1			# => "expression"
	defined? dummy		# => nil
	defined? printf		# => "method"
	defined? String		# => "contant"
	defined? $_			# => "global-variable"
	defined? Math::PI # => "constant"
	defined? a = 1 		# => "assignment"
	defined? 42.abs 	# => "method"
	defined? nil		# => "nil"
#####Comparing Objects
除了boolean运算符，Ruby对象支持用方法==，===，<=>, =~,eql?和equal?来比较,<=>定义在Object类中,但通常被重写来实现类希望的语义,比如Array类重定义了==所以两个array对象只有在它们的元素个数一样，并且相应的元素也相等的情况下才真正的相等
==和=~都有相应地否定形式,!=和!~,如果调用!=或!~,Ruby首先寻找此类的!=或!~,如果发现就调用它们，如果不发现，就调用==或=~,然后再对结果取反
	class T
		def ==(other)
				puts "Comparing self == #{other}"
			other == "value" 
			end
	end
	t = T.new
	p (t == "value") 
	p (t != "value")
	produces:
	Comparing self == value
	true
	Comparing self == value
	false

以上调用`t != "value"`时,首先调用t = "value",然后再对结果取反
各操作符的意义如下

{% img /images/2014/7/operator.png%}

#####if and unless Expressions
if then语句

	if artist == "Gillespie" then handle = "Dizzy"
	elsif artist == "Parker" then handle = "Bird"
	else
	handle = "unknown" 
	end
关键字`then`是optional的，如果你想将语句多行排列的话
	if artist == "Gillespie" 
		handle = "Dizzy"
	elsif artist == "Parker" 
		handle = "Bird"
	else
	handle = "unknown" 
	end
不过如果你想将then后对应的语句与if语句放在同一行，then是必不可少的!
	if artist == "Gillespie" then handle = "Dizzy" 
	elsif artist == "Parker" then handle = "Bird" 
	else handle = "unknown"
	end
与if对应的是unless
	unless duration > 180 
		listen_intently
	end
#####if and unless Modifiers
	mon, day, year = $1, $2, $3 if date =~ /(\d\d)-(\d\d)-(\d\d)/ 
	puts "a = #{a}" if $DEBUG
	print total unless total.zero?
	File.foreach("/etc/passwd") do |line|
	next if line =~ /^#/ # Skip comments 
	parse(line) unless line =~ /^$/ # Don't parse empty lines
	end
#####case Expressions
* 第一种形式
		case
			when song.name == "Misty"
			puts "Not again!"
			when song.duration > 120
			puts "Too long!"
			when Time.now.hour > 21
			puts "It's too late" 
			else
			song.play
			end
	* 第二种形式			case command 
		when "debug"
		  dump_debug_info
		  dump_symbols 
		when /p\s+(\w+)/
		dump_variable($1) 
		when "quit", "exit"
		exit
		else
		print "Illegal command: #{command}" 
		end
* 第三种形式
与then语句结合使用
	
		kind = case year
		when 1850..1889 then "Blues"
		when 1890..1909 then "Ragtime"
		when 1910..1929 then "New Orleans Jazz" when 1930..1939 then "Swing"
		else "Jazz"
		end
case的比较是用 `===`来比较的,所以，只要类为`===`定义了有意义的语义,此类的对象就能用在case语句中
需要`注意`的是,ruby的类是`Class`类的对象，===已经在Class类中定义了，可以用来测试相应的argument是否是receiver的instance或它的superclasses之一，所以你可以测试对象的类
	case shape
	when Square, Rectangle
	# ...
	when Circle
	# ...
	when Triangle 
	# ...
	else
	# ...
	end
#### Loops
while与until表达的意义相反
	while line = gets # ...
	end		
	until play_list.duration > 60 

	play_list.add(song_list.pop)
	end
	a=1
	a*=2 whilea<100 a # => 128
	a -= 10 until a < 100 a # => 98
	
range用作条件语句
	file = File.open("ordinal") 
	while line = file.gets
		puts(line) if line =~ /third/ .. line =~ /fifth/ 
	end
	produces:
    third
    fourth
    fifth
    
range中的起始位置也可以是表达式
    File.foreach("ordinal") do |line|		if (($. == 1) || line =~ /eig/) .. (($. == 3) || line =~ /nin/)		print line		end 
	end
	produces:
	first
	second
	third
	eighth
	ninth如果你用`begin.....end`block，则不论while的条件语句是什么，都会`至少`执行一次
	print "Hello\n" while false 
	begin
		print "Goodbye\n" 
	end while false
	produces:
	Goodbye
####Iterators

	3.times do
	
	print "Ho! "
	end
	produces:
	Ho! Ho! Ho!
upto有使用
	0.upto(9) do |x|
	print x, " "
	end
	produces:
	0123456789step
	0.step(12, 3) {|x| print x, " " } 
	produces:
	0 3 6 9 12
array的遍历
	[ 1, 1, 2, 3, 5 ].each {|val| print val, " " } 
	produces:
	11235
each

	File.open("ordinal").grep(/d$/) do |line| 

		puts line
	end
	produces:
	second
	third

loop的使用
	
	loop do		# block ...	end
for...in
	
	for song in playlist

	song.play
	end

如果按以上这么写，则Ruby会将它翻译成

	playlist.each do |song|

		song.play
	end你可以将for....in用在任何定义了each的方法的类上
	for i in File.open("ordinal").find_all {|line| line =~ /d$/} 
		print i.chomp, " "
	end
	
	class Periods 
		def each
		yield "Classical" 
		yield "Jazz" 
		yield "Rock"
		end 
	end
	periods = Periods.new 
	for genre in periods
	print genre, " " end
	produces:
	Classical 
	Jazz 
	Rock
#####break,redo,next
break和next后面可以跟一个value

	result = while line = gets
				break(line) if line =~ /answer/
		 	 end process_answer(result) if result
####作用域
注意以下代码中,a的赋值语句虽然没有执行，但在之后的语句中a仍然可用
	a = "never used" if false 
	[99].each do |i|
		a = i # this sets the variable in the outer scope 
	end
	a # => 99