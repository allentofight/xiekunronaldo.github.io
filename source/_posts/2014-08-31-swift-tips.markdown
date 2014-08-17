---
layout: post
title: "Swift Tips"
date: 2014-08-31 14:25:53 +0800
comments: true
categories: Swift
---
* `..<`等价于`..`

```
let possibleTipsInferred = [0.15, 0.18, 0.20]
for i in 0..<possibleTipsInferred.count {
  let possibleTip = possibleTipsInferred[i]
  println("\(possibleTip*100)%: \(calcTipWithTipPct(possibleTip))")
}
```
* Dictionary返回类型

```
func returnPossibleTips() -> [Int: Double] { 
}
```
* 命名空间
[参考](http://www.raywenderlich.com/74904/swift-tutorial-part-2-simple-ios-app)

```
import UIKit
 
class ViewController: UIViewController {
 
  override func viewDidLoad() {
    super.viewDidLoad()
    // Do any additional setup after loading the view, typically from a nib.
  }
 
  override func didReceiveMemoryWarning() {
    super.didReceiveMemoryWarning()
    // Dispose of any resources that can be recreated.
  }
```
This is the first example you’ve seen of a class that subclasses another class. Here, you are declaring a new class ViewController that subclasses Apple’s UIViewController.

Experienced iOS developers – note that you do not have to put a class prefix on your class names like you did in Objective-C to avoid namespace collisions (i.e. you don’t have to name this RWTViewController). This is because Swift has namespace support, and the classes you create in your project are in their own namespace.
To see what I mean, replace the class declaration with the following:

```
class UIViewController {
}
 
class ViewController: UIKit.UIViewController {
}
```

Here UIKit.UIViewController refers to the UIViewController class in the UIKit namespace. Likewise, TipCalculator.UIViewController would refer to the the UIViewController class in your project.
* String to Double

```
 Double((totalTextField.text as NSString).doubleValue)
```
`注意`:At the time of writing this tutorial, Swift’s String class does not have access to every method that NSString has (NSString is the string class in the Foundation framework). In particular, Swift’s String class does not have a method to convert to the string to a double; however NSString does.
You can call (xxx as NSString)() on a Swift String to convert it to an NSString. Then, you can call any method that is available on NSString, such as a method to convert to a double.
* sort dict keys

```
var keys = Array(possibleTips.keys)
sort(&keys)
for tipPct in keys {
  let tipValue = possibleTips[tipPct]!
  let prettyTipValue = String(format:"%.2f", tipValue)
  results += "\(tipPct)%: \(prettyTipValue)\n"
}
```
* Class Prefixes

	Swift types are all automatically namespaced by the module that contains them. As a result, prefixes are not required in order to minimize naming collisions. If two names from different modules collide you can disambiguate by prefixing the type name with the module name:

	You should not add prefixes to your Swift types.

	If you need to expose a Swift type for use within Objective-C you can provide a suitable prefix (following our Objective-C style guide) as follows:

```
@objc (RWTChicken) class Chicken {
   ...
}
```

* Value Types and Reference Types

	Instances of value types are copied whenever they’re assigned or used as a function argument. Numbers, strings, arrays, dictionaries, enums, tuples, and structs are value types. For example:

```
	var a = "Hello"
	var b = a
	b.extend(", world")
	println("a: \(a); b: \(b)") // a: Hello; b: Hello, world
```
* xcode 6.1 beta版

现在，可以直接在构造器中返回nil表示构造失败。一个表示失败的构造器可以通过init?来声明，它可以返回一个显式的可选类型或隐式解封的可选类型。但要注意对于枚举和结构体来说随时都可以返回nil，而对类则不一样，只有当所有的存储属性被初始化后或者其self.init和super.init被调用后才可以返回nil，当前调用代码就可以收到nil，来判断构造是否成功：

```
extension Int {
init?(fromString: String) {
if let i = fromString.toInt() {
// Initialize
self = i
} else {
// Discard self and return 'nil'.
return nil
    }
  }
}
```
可以这么使用：

```
if let twentytwo = Int(fromString: "22") {
println("the number is \(twentytwo)”)
} else {
println("not a number”)
}
```
* Calling on Methods
When encapsulated in a class (or struct or enum), the first parameter name of a method is not included externally, while all following parameter names are included externally when the method is called:

```
class MyFunClass {
    
    func hello(name: String, age: Int, location: String) {
        println("Hello \(name). I live in \(location) too. When is your \(age + 1)th birthday?")
    }
    
}
let myFunClass = MyFunClass()
myFunClass.hello("Mr. Roboto", age: 5, location: "San Francisco")
```
It is therefore best practice to include your first parameter name in your method name, just like in Objective-C:

```
class MyFunClass {
    
    func helloWithName(name: String, age: Int, location: String) {
        println("Hello \(name). I live in \(location) too. When is your \(age + 1)th birthday?")
    }
    
}
let myFunClass = MyFunClass()
myFunClass.helloWithName("Mr. Roboto", age: 5, location: "San Francisco")
```
If for some special reason you want to skip the external parameter names in your function (I’d recommend having a very good reason for doing so), use an _ for the external parameter name:

```
class MyFunClass {
    
    func helloWithName(name: String, _ age: Int, _ location: String) {
        println("Hello \(name). I live in \(location) too. When is your \(age + 1)th birthday?")
    }
    
}

let myFunClass = MyFunClass()
myFunClass.helloWithName("Mr. Roboto", 5, "San Francisco")
```
* Instance Methods Are Curried Functions
One cool thing to note is that instance methods are actually curried functions in Swift:

		The basic idea behind currying is that a function can be partially applied, meaning that some of its parameter values can be specified (bound) before the function is called. Partial function application yields a new function.

So given that I have a class:

```
class MyHelloWorldClass {
    
    func helloWithName(name: String) -> String {
        return "hello, \(name)"
    }
}
```

I can create a variable that points to the class’s helloWithName function:

```
let helloWithNameFunc = MyHelloWorldClass.helloWithName
```
My new helloWithNameFunc is of type MyHelloWorldClass -> (String) -> String, a function that takes in an instance of my class and returns another function that takes in a string value and returns a string value.
So I can actually call my function like this:

```
let myHelloWorldClassInstance = MyHelloWorldClass()

helloWithNameFunc(myHelloWorldClassInstance)("Mr. Roboto") 
// hello, Mr. Roboto
```
* Init: A Special Note
A special init method is called when a class, struct, or enum is initialized. In Swift, you can define initialization parameters, just like with any other method:

```
class Person {
    
    init(name: String) {
        // your init implementation
    }
    
}

Person(name: "Mr. Roboto")
```

Notice that, unlike other methods, the `first parameter` name of an init method is required externally when the class is instantiated.

It is best practice in most cases to add a different external parameter name—fromName in this case—to make the initialization more readable:

```
class Person {
    
    init(fromName name: String) {
        // your init implementation
    }
    
}

Person(fromName: "Mr. Roboto")
```
And of course, just like with other methods, you can add an _ if you want your init method to skip the external parameter name.

```
struct Celsius {
    var temperatureInCelsius: Double
    init(fromFahrenheit fahrenheit: Double) {
        temperatureInCelsius = (fahrenheit - 32.0) / 1.8
    }
    init(fromKelvin kelvin: Double) {
        temperatureInCelsius = kelvin - 273.15
    }
    init(_ celsius: Double) {
        temperatureInCelsius = celsius
    }
}

let boilingPointOfWater = Celsius(fromFahrenheit: 212.0)
// boilingPointOfWater.temperatureInCelsius is 100.0

let freezingPointOfWater = Celsius(fromKelvin: 273.15)
// freezingPointOfWater.temperatureInCelsius is 0.0

let bodyTemperature = Celsius(37.0)
// bodyTemperature.temperatureInCelsius is 37.0
```
* Optional Parameter Types
Optionals are similar to using nil with pointers in Objective-C, but they work for any type, not just classes. Optionals are safer and more expressive than nil pointers in Objective-C and are at the heart of many of Swift’s most powerful features.

* Parameters with Default Values

```
func hello(name: String = "you") {
    println("hello, \(name)")
}

hello(name: "Mr. Roboto")
// hello, Mr. Roboto

hello()
// hello, you
```

Note that a parameter `with a default value` automatically has an external parameter name.

* Variadic Parameters

```
func helloWithNames(names: String...) {
    for name in names {
        println("Hello, \(name)")
    }
}

// 2 names
helloWithNames("Mr. Robot", "Mr. Potato")
// Hello, Mr. Robot
// Hello, Mr. Potato

// 4 names
helloWithNames("Batman", "Superman", "Wonder Woman", "Catwoman")
// Hello, Batman
// Hello, Superman
// Hello, Wonder Woman
// Hello, Catwoman
```
The catch here is to remember that it is possible to pass in 0 values, just like it is possible to pass in an empty array, so don’t forget to check for the empty array if needed:

```
func helloWithNames(names: String...) {
    if names.count > 0 {
        for name in names {
            println("Hello, \(name)")
        }
    } else {
        println("Nobody here!")
    }
}

helloWithNames()
// Nobody here!
```
Another note about variadic parameters: the variadic parameter must be the last parameter in your function’s parameter list!

* Functions as Parameters
In Swift, functions can be passed around just like variables. For example, a function can have another function passed in as a parameter:

```
func luckyNumberForName(name: String, #lotteryHandler: (String, Int) -> String) -> String {
    let luckyNumber = Int(arc4random() % 100)
    return lotteryHandler(name, luckyNumber)
}

func defaultLotteryHandler(name: String, luckyNumber: Int) -> String {
    return "\(name), your lucky number is \(luckyNumber)"
}

luckyNumberForName("Mr. Roboto", lotteryHandler: defaultLotteryHandler)
// Mr. Roboto, your lucky number is 38
```
Instance methods can also be passed in a similar way:

```
func luckyNumberForName(name: String, #lotteryHandler: (String, Int) -> String) -> String {
    let luckyNumber = Int(arc4random() % 100)
    return lotteryHandler(name, luckyNumber)
}

class FunLottery {
    
    func defaultLotteryHandler(name: String, luckyNumber: Int) -> String {
        return "\(name), your lucky number is \(luckyNumber)"
    }
    
}

let funLottery = FunLottery()
luckyNumberForName("Mr. Roboto", lotteryHandler: funLottery.defaultLotteryHandler)
// Mr. Roboto, your lucky number is 38
```
To make your function definition a bit more readable, consider type-aliasing your function (similar to typedef in Objective-C):

```
typealias lotteryOutputHandler = (String, Int) -> String

func luckyNumberForName(name: String, #lotteryHandler: lotteryOutputHandler) -> String {
    let luckyNumber = Int(arc4random() % 100)
    return lotteryHandler(name, luckyNumber)
}
```
You can also have a function without a name as a parameter type (similar to blocks in Objective-C):

```
func luckyNumberForName(name: String, #lotteryHandler: (String, Int) -> String) -> String {
    let luckyNumber = Int(arc4random() % 100)
    return lotteryHandler(name, luckyNumber)
}

luckyNumberForName("Mr. Roboto", lotteryHandler: {name, number in
    return "\(name)'s' lucky number is \(number)"
})
// Mr. Roboto's lucky number is 74
```
* Return a Function
Any function can also return a function in Swift:

```
func myFuncThatReturnsAFunc() -> (Int) -> String {
    return { number in
        return "The lucky number is \(number)"
    }
}

let returnedFunction = myFuncThatReturnsAFunc()

returnedFunction(5) // The lucky number is 5
```

To make this more readable, you can of course use type-aliasing for your return function:

```
typealias returnedFunctionType = (Int) -> String

func myFuncThatReturnsAFunc() -> returnedFunctionType {
    return { number in
        return "The lucky number is \(number)"
    }
}

let returnedFunction = myFuncThatReturnsAFunc()

returnedFunction(5) // The lucky number is 5
```
* Nested Functions

```
func myFunctionWithNumber(someNumber: Int) {

    func increment(var someNumber: Int) -> Int {
        return someNumber + 10
    }
    
    let incrementedNumber = increment(someNumber)
    println("The incremented number is \(incrementedNumber)")
}

myFunctionWithNumber(5)
// The incremented number is 15
```
* [Functional APIs with Swift](http://www.objc.io/issue-16/functional-swift-apis.html)
	* The Filter Type

	```
	typealias Filter = CIImage -> CIImage
	```
it may seem strange to use the name Filter for a function type. Usually, we’d use such a name for a class, and the temptation to somehow denote the function nature of this type is high. We could name it FilterFunction or something similar. However, we consciously chose the name Filter, since the key philosophy underlying functional programming is that functions are just values. They’re no different from structs, integers, tuples, or classes. It took me some getting used to as well, but after a while, it started to make a lot of sense.
	* Building Filters
* Class 和 Struct的区别
来看以下demo

```
struct MyStruct {
  let t = 12
  var u: String
}

let struct2 = MyStruct(t: 12, u: "World")
struct2.u = "Planet" // Error: struct2 is immutable
struct2 = MyStruct(t: 10, u: "Defeat") // Error: struct2 is an immutable ref

class MyClass {
  let t = 12
  var u: String

  init(t: Int, u: String) {
    self.t = t
    self.u = u
  }
}

let class2 = MyClass(t: 12, u: "World")
class2.u = "Planet" // No error
class2 = MyClass(t: 11, u: "Geoid") Error: class2 is an immutable reference

```

注意以上`struct2.u = "Planet"`和`class2.u = "Planet"`的区别
因为struct是`值类型`,class是`引用类型`

* Array

Swift arrays aren't like this - they are value types instead of reference types. This means that they behave like a struct, not a class. Therefore, the let or var keyword not only specifies whether or not the variable can be redefined, but also whether or not the created array is mutable.

```
var array1 = [1,2,3,4]
array1.append(5)  // [1,2,3,4,5]array1[0] = 27    // [27,2,3,4,5]
array1 = [3,2]    // [3,2]

let array2 = [4,3,2,1]
array2.append(0) // Error: array2 is immutablearray2[2] = 36   // Error: array2 is immutable
array2 = [5,6]   // Error: cannot reassign an immutable reference
```
* AnyObject
AnyObject behaves almost exactly the same as id in objective-C. The difference is that properties and methods which take no arguments will return nil if that method/property doesn't exist on the AnyObject:

```
let myString: AnyObject = "hello"
myString.cornerRadius // Returns nil
```
还可以有以下用法

```
func someFunc(parameter: AnyObject!) -> AnyObject! {
  if let castedParameter = parameter as? NSString {
    // Now I know I have a string :)
    ...
  }
}

func someArrayFunc(parameter: [AnyObject]!) {
  let newArray = parameter as [String]
  // Do something with your strings :)
}
```
* Protocol Conformance

```
protocol MyProtocol {
  func myProtocolMethod() -> Bool
}
```
In order to check conformance of a protocol that protocol must be an objective-C protocol - and annotated with @objc:

```
@objc protocol MyNewProtocol {
  func myProtocolMethod() -> Bool
}

if let class1AsMyNewProtocol = class1 as? MyNewProtocol {
  // We're in
}
```
* Double Optional

```
var twice_optional: String?? = Optional.Some(nil)
```
* Debugger

```
(lldb) fr v -R twice_optional
(lldb) fr v -d r creature

```
* Language can be changed
在lldb中,对oc来说,$0,$1....保存了之前的表达式,而在swift文件中则是$R0, $R1....
,可能断点在swift文件中停住了,此时调用[$0 class]可能会报错，可以用以下表达式

```
expr -l objc++ -- (Class)[$0 class]
```
表指示lldb来按oc的方式调试,但这么做的话导致的一个后果是`locals will not be available`

当我们用po来打印对象时
* Swift对象display using formatters
* OC对象调用 -description方法

假设有以下一个对象

```
class MyObject: NSObject {
	var myInt = 1
	override var description: String! {
		return "Hello Swift subclass. myInt = \(myInt)"
	}
}
```
如果有一个MyObject的对象objc,在lldb中调用`po object`命令，会出现以下结果

```
(lldb) po object
0x0000000100700ea0
	(ObjectiveC.NSObject) = {}, myInt = 1)
```
如何让`po object`调用object的description方式

```
expr -l objc++ -o -- 0x0000000100700ea0
```
出现了

```
Hello Swift subclass. myInt = 1
```
* Data Formatters for Swift Objects

假设有以下struct

```
struct Address {
	var name: String
	var city: String
	var zip: Int
	var state: State
}
```
假设有一个Address的对象enrico,如果 `po enrico`则显示 

```
{
	Name = "Enrico Granata"
	City = "Mountain View"
	Zip = 94043
	State = California
}
```
可以按固定的格式显示，如下

```
(lldb) type summary add -s "${var.name} \n ${var.city} \n ${var.zip}, ${var.state}"  projectname.Address
```
`注意必须在类前加上module`
此时如果再`po enrico`

```
"Enrico Granata"
"Mountain View"
94043, California
```
* name uniqueness
假设有一个Module1.swift的文件,里面有一个`class MyClass {...}`,编译后命名为`_TtC7Module17MyClass`
如果你碰到了一个以上的mangled name(比如在碰到crash的时候控制台可能会出现此mangle name)该怎么办,可以用以下命令

```
xcrun swift-demangle _TF5MyApp6myFuncFTSiSi_TSS_
```
出现以下结果

```
_TF5MyApp6myFuncFTSiSi_TSS_ ---> MyApp.myFunc (Swift.Int, Swift.Int) -> (Swift.String)
```

* @objc的妙用

```
@objc protocol WeeklyWeatherContainer {	var dailyWeather: [DailyWeather] { get set }}
```
 This protocol includes the @objc attribute as required for runtime conformance checking – i.e. checking that a particular object adopts a protocol at runtime, as opposed to compile time.