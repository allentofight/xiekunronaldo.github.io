---
layout: post
title: "C++ &amp; Objective C"
date: 2014-06-24 07:58:39 +0800
comments: true
categories: 编程语言
---
译自[C++ vs Objective C](http://paralaxer.com/cpp-vs-objective-c/)

##  C++ 是一辆手动档汽车

如果说object C是一辆自动档汽车，那么C++就是一辆手动档的汽车

也许有人会说自动档汽车更好，因为它更容易操纵，不过,用自动档的人可能会在经过一条石子路时因没有将离和器置空档的经验而无法让车停下来

C的运行速度更快，也能对它进行更多的掌控，然而相应得，它对程序员的技能提出了更高的要求

比如，C++在解引用指针前不检查指针(是否为空或有效)来节省时间，这提升了你游戏的运行速度,给了你更多的掌控，同时它也使得你必须在debug模式下使用用assert

## 发送消息给空对象

我们来看一下ObjC和C++的一个最根本的区别之一

用Object C,你能安全地发送信息给nil对象(付出的代价是程序的执行时间会慢一点)

	// in Objective C
	Node* node = nil;
	 
	// this will not crash, it will just do nothing
	[node setPosition:Point(10,10)];
	
在C++中，如果你解引用一个空指针，将会产生一个运行时错误

	// in C++
	Node* node = nullptr; // nullptr introduced in C++11
	
	// this will crash
	node->setPosition(Point(10,10));
	
	// using a safeguard, this will not crash
	if(node)
	   node->setPosition(Point(10,10));
	
	// using an assertion will trigger a debuggable error
	assert(node);
	node->setPosition(Point(10,10));
	
在底层，Objc实际上实现了一个空对象，对任何发送给它的消息，它都返回0，这种实现方式让编程更安全，也更简单，不过，这么做也会导致一些bug难以察觉

C++在解引用前不会检查一个指针是否为空,如上所示,如果node为null,C++会用null去执行setPosition函数,如果通过一个null指针去访问它的成员变量,你的程序将会崩溃

一种解决方法是用[断言](http://www.cplusplus.com/reference/cassert/assert/),它仅在非发布模式下起作用,如果传给断言的表达式为假，程序终止，如果此时有个调试器，程序终止时将停在断言失败的那一行，这样，你就能看到哪一行代码出问题了

	Node* node = nullptr;
	
	// this will pause execution when debugging
	assert(node);
	
由于断言通常被定义为宏,用它的一个好处是在发布模式下(或者其他任何定义了NDEBUG的模式中)，此断言宏不做任何事情,本质上,编译器不会为它产生任何二进制的代码，这样，在debug模式时，你就可以放心地用它,同时,也保证了发布模式下的程序运行速度(由于针对assert的二进制代码被忽略掉了)

## 可读性
我们来看看两者的另一个基本的区别,Object C生来就比C++有更好的可读性,因为方法的参数名称也集成到了方法名中

	// Objective C
	[layer addChild:sprite z:1];
	
	// C++
	layer->addChild(sprite, 1);
	
你能看到两者在可读性上的差别吗?

Objective C有更好的可读性因为addChild方法中的第二个参数清楚地显示了z参数,在C++中,没人知道1代表什么

如今,像Xcode之类IDE提供了自动补全的功能，这也减轻了你写代码的负担，当你敲addChild时，IDE们会自动为你显示参数类型和名字,这样IDE就为你清楚地显示了相应的参数是什么(类型和名字),不过如果你好久没碰你的项目了，然后回头看addChild方法的时候，你就会感到困惑，不明白第二个参数代表什么意思

用一些名字定义良好的变量，你可以让C++代码更具有可读性

	int z = 1;
	layer->addChild(sprite, z);
	
## 速度和可移植性

我们已经讨论了C++为何比Objective C更快,那么可移植性呢,这才是C++闪光的地方

C++比OC更具有可移植性,它可以在多个平台上(如Windows,Mac, Linux,iOS,Android等平台上)被原生的编译集成

说到Android,如果你升级NDK的GCC编译器，技术上是可以编译OC的,但要注意，你需要所有的Foundation 类

还记得 NSObject, NSString, NSArray否？它们在Mac上完美的实现了，而在Windows上则没有,所有这些都必须用一种跨平台的方式重新实现

幸运的是,有一些像[Cocotron](http://www.cocotron.org)这样的项目致力于此,然而如果到一些android相关的具体问题上,比如输入和声音,你仍然需要一个跨平台的库, 这些库实现了相应平台的细节，所以你就不需要做了.

这就是为什么C++和Cocos2dX 如此cool的原因,cocos2dx处理了平台相关的问题,并且用C++来实现它们

除了速度，可移植性和可读性，我们来看看更具体的区别,比如oc的dClass和id类型

## Class和id类型
OC有一个叫Class类型的实现,它允许你获得对象的类并且将你存储在一个变量中.

C++并没有相应的Class类型的实现方式，但它有一个叫做运行时类型信息(RTTI)的东西,可以粗略地将一个指针转换为一个字符串,这个字符串包含有一个以[typeid](http://www.cplusplus.com/reference/typeinfo/type_info/)表示的对象类,不过,由于各个平台的转换结果不同,这种解决方式似乎并不可靠

OC的Class类型使得从字符串创建类可以变得相当优雅，考虑以下情况

	// Objective C
	NSString* string = @"Hammer";
	Class class = NSClassFromString(string);
	id object = [[class alloc] init];
	
	// C++
	Tool* tool = nullptr;
	std::string string = "Hammer";
	if( string == "Hammer" )
	   tool = new Hammer;
	   
以上显示了OC的类类型的优雅以及在C++中相应的可能的解决方式

你可以看到以上代码中我们用了ObjC的id类型,它本质上是一个指向对象类型的指针,大致相当于C++中的void*,但仍有不用两者的许多理由

## 惰性

假设有一个叫做Parent的父类,有一个继续它的Child子类，同时有一个叫做goToSleep的方法

在C++中,如果你在Child中重新定义了Child的接口，你就不得不在Child的接口中(头文件中)重新定义goToSleep这个方法，但在OC中，你无需这么做，只要在@implementation中实现它即可

	/ Objective C
	@interface Parent : NSObject
	   -(void) goToSleep;
	@end
	
	@interface Child : Parent
	   // woo! don't have to redefine goToSleep
	@end
	
	@implementation Parent
	   -(void) goToSleep {}
	@end
	
	@implementation Child
	   -(void) goToSleep {}
	@end
	
	// C++
	class Parent
	{
	   public:
	      virtual void goToSleep();
	};
	
	class Child : public Parent
	{
	   public:
	      // goToSleep has to be redefined
	      // if it is to be overridden
	      virtual void goToSleep();
	};
	
	void Parent::goToSleep() {}
	
	void Child::goToSleep() {}

C++之所以迫使程序员重新定义被重写的函数的原因是因为这么做可以让编译器的工作变得更加容易，编辑器无需因为一些神秘的方法去检查类的继承关系

## 虚函数

在以上的例子中,我们看到了C++中virtual关键字的使用,当virtual被用在函数时，位于继承层次中最低的那个类有最高的优先级调用此函数(比如以上例子中，Child的goToSleep先被调用)

在OC中,所以的方法本质上是虚函数,这让编程变得简单，不过相应的，这么做也导致了[性能的下降](http://stackoverflow.com/questions/4882105/c-polymorphism-inheritance-question-redefinition-of-base-functions-vs-virtual/4882155#4882155)

另外，如前所述，C++相当于手动档汽车,你可以根据需要指定或不指定函数为virtual

## 栈

在OC中，你只能在堆上分配对象
在C++中，你可以在栈上分配对象，有栈上创建对象比在堆上创建快，同时也保证了，对象超出相应的作用范围时析构函数能被调用,即便在已经抛出异常的情况下

举个例子

	// Objective C
	// 仅分配在堆上
	Tool* tool = [[Tool alloc] init];
	
	// C++
	//我们采用了快速的，可信任的栈分配
	Tool tool;
	
## 运算符重载
在C++中，你可以重载运算符,比如，如果你写了一个Vector对象,你可以重载"+"符号,这样Vector的对象就能进行相加的运算了

	// C++
	class Vector
	{
	   const Vector operator+(const Vector& v) const
	   {
	      return Vector(x + v.x, y + v.y, z + v.z);
	   }
	}
	
	// this '+' calls the overloaded operator+
	// isn't it nice?
	Vector v1,v2,v3;
	v3 = v1 + v2;

而在OC中，则不能重载运行符，不过你可以在方法命名上动些脑筋

	// Objective C
	v3 = [v1 addVector:v2];
	
不过，这样一来，OC在处理一些事情时便显得有些哆嗦了，如下

	// Objective C
	NSString* a = @"something";
	NSString* b = @"else";
	if( [a isEqualToString:b] )
	   [self doSomething];
	   
	// C++
	string a = "something";
	string b = "else";
	if( a == b )
	   doSomething();
	   
## 私有性

OC在技术上是没有私有方法的，不过我们可以在.m文件中定义一个空的类别，并在此空的类别中定义此方法(来模拟私有方法),达种方法是很优雅的,因为这样一来私有方法就无需在公共接口中声明了(因此由于头文件的变化，你的代码将会重新进行索引和编译)

	// 在OC的头文件中 (.h)
	@interface Something : NSObject
	   // See? No private method
	@end
	
	// 在实现文件中 (.m)
	// 用一个类别来扩展接口
	// (空类别代表私有的一些属性)
	@interface Something () // a blank category
	   -(void) privateMethod;
	@end
	
在C++中，有公有和私有的概念，不过私有的属性或方法必须在公有接口另声明

如果你确实不想在公有接口中声明私有的方法，你可以采用在.m文件中声明静态函数的形式来避免它(这和之前提到的OC的空类别有点类似)

## 类扩展

如上例所示，OC可以用一个类别来扩展一个类

类别是很优雅的，因为一个类可以在任何地方被扩展,如在.h和.m文件中，或是其他地方

技术上C++是不能扩展一个类的，不过它有[多继承](http://www.cprogramming.com/tutorial/multiple_inheritance.html)

## 默认参数
C++还有一个很cool的地方:默认参数.函数中的参数可以有一个默认值,这样我们可以选择在调用函数时是否给此参数传值 

	// C++
	class Something
	{
	   void doSomething(int i, float f = 0.0f) {}
	};
	
	Something s;
	
	// can be called with or without the f parameter
	s.doSomething(5);
	s.doSomething(5, 1.0f);
	

OC则没有默认参数，不过它可以采用定义多个方法的形式来实现

	// Objective C
	@interface Something : NSObject
	   -(void) doSomething:(int)i;
	   -(void) doSomething:(int)i withF:(float)f;
	@end
	
	@implementation Something
	   -(void) doSomething:(int)i
	   {
	      [self doSomething:i withF:0.0f];
	   }
	   
	   -(void) doSomething:(int)i withF:(float)f
	   {
	      // do something with i and f...
	   }
	@end
	
	Something* s = [[Something alloc] init];
	
	// either method can be called, however
	// two separate methods have been implemented
	[s doSomething:5];
	[s doSomething:5 withF:1.0f];
	
##结论:混合使用C++和OC
以上就是C++和OC的主要区别
需要记住两者总是可以共存的，实际上你可以采用[Objecttive C++](http://en.wikipedia.org/wiki/Objective-C#Objective-C.2B.2B)的使用来让两者共存

你可以将.m文件改为.mm文件，这样编辑器就知道是用Object C++了，从而可以使用Object C,C++或者两者混用

	// In the file AppDelegate.mm
	@implementation AppDelegate
	   -(void) applicationDidFinishLaunching:(NSNotification*)aNotification
	   {
	      // Do some Objective C stuff
	      window = [[Window alloc] initWithContentRect:...];
	      
	      // Now switch to C++ and launch Cocos2dx
	      Application::getInstance()->run();
	   }
	@end

如以上例子所见,Objective C++是很常见的,它对于在同一个方法中响应iOS和Mac的通知并启动Cocos2d-x是必须的