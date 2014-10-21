---
layout: post
title: "The Class System"
date: 2014-10-05 20:25:39 +0800
comments: true
categories: Sencha Touch
---
# The Class System
上一章，我们学习了如何从已知的component classes中创建components并引用它们,我们其实也可以通过extending Sencha Touch的base classes来定义我们自己`custom component classes`,接下来我们将学习如何创建一个component并且定义我们自己的类,本章中，我们将学习到以下知识点

* How to define your own custom class* How to define getters and setters* How to define singletons and static members How inheritance works (extending)* How multiple inheritance works (mixins)
#### 定义我们自己的类let’s discuss how the Sencha class system works and how to define your own blueprints to define a class.
Let’s make a blueprint containing a variable, myVar, and a method, myMethod. Using Ext.define():
```javascript
Ext.define('AppName.packagename.ClassName', { 
	//class configuration object	myVar: 1,	myMethod: function(name){		//console.log("Log: " + name);	} },function(){	//optional callback used for logging});```
It’s important to know that components in the Sencha framework are, in fact, classes.To create a class definition, you will use the Ext.define method.
In general, the string class name consists of the following parts:

```javascript
AppName.packagename.ClassName
```For example, the following class maps to the file app/view/ListView.js:

```
Ext.define('MyDemoApp.view.ListView', { 
	//class configuration object
});
```
In the preceding code, note the following:

* The app name, MyDemoApp, maps to the app folder in your project root and should be written in the upper CamelCase notation (wherein each word begins with a capital letter).* The package name, view, maps to the package folder in the app folder; package names are always written in lowercase.* The class name, ListView, should also be written in the upper CamelCase notation. The class name should contain the same name as the JavaScript filename (ListView.js); therefore, it’s not possible to put multiple Sencha Touch classes in a single JavaScript file.
The second argument takes a class configuration object. This is where you can set properties and methods. 
```
Ext.application({ 
	name: 'DemoApp',	launch: function() {	/* Start class definition code: */	//Create a class definition Ext.define('DemoApp.view.DemoComponent', { //	//		extend: 'Ext.Component', config: {		html: 'Hello World' // }	}, function() {		console.log("class is created"); //	});	//Create a class instance 
	Ext.create('DemoApp.view.DemoComponent', { //		fullscreen: true 	});	} });```
#### Defining Getters and Setters
the Sencha class system can automatically create accessors and mutators (known as magic methods) for you.
It is very easy to create getter and setter methods to access or mutate a class property; they will be automatically generated for you. That is why some people call them magic.
To autocreate magic getter and setter methods, set a property in the class config:
```
config: {	myProperty: "some value"}```The config object autocreates the getter and setter methods as follows:

```
getMyProperty: function(){	return this.myProperty; //returns "some value"}
setMyProperty: function(x){	this.myProperty = x; }
```
Besides getter and setter methods, it also automatically creates apply and update methods. These methods are handy to change the process, before and after you set a value:

```
applyMyProperty: function(x){	//runs before this.myProperty changes. //for example validation}updateMyProperty: function(x){	//runs after this.myProperty was changed.}
```
来看一个例子，在configs里定义了一个Cab类，注意getDriver(), setDriver(), applyDriver(), and updateDriver() 是自动产生的.为了在值生产前作一些校验，你需要重写 applyDriver() 方法; 为了在改变driver后再作一些操作，你需要重写updateDriver()方法

```
Ext.define('VehicleApp.vehicle.Cab', { // The default config	config: {		driver: 'John Doe', 		driver2: {			firstName: 'John',			lastName: 'Doe' 		}	},	constructor: function(config) {		 this.initConfig(config);	},	applyDriver: function(newVal){ 
		if(newVal === 'The Pope') {			console.log(newVal + " is an invalid taxi driver.");			return; 		}		return newVal; 	},	updateDriver: function(newVal, oldVal){		console.log('The owner has been changed from ' + oldVal + ' to ' + newVal);	} });
```
Because the previous code example does not extend from a Sencha component, I had to initialize the config settings in my constructor:

```
constructor: function(config) { 
	this.initConfig(config);}
```
The config object sets some default values. When you are not extending from an Ext.Component, you have to call the initConfig(config) method once by yourself,(e.g., in the base class), which will initialize the configuration for the class that was passed in while creating the objects.
After you instantiate the class, you have access to the getters and setters in the prototype. They have been magically generated:

```
var taxi = Ext.create("VehicleApp.vehicle.Cab", { 
	driver: "John Doe"});alert(taxi.getDriver()); //alerts 'John Doe';taxi.setDriver('The Pope');alert(taxi.getDriver()); //alerts 'John Doe' because 'The Pope' is invalid.//changes the driver from 'John Doe' to 'Lee Boonstra'taxi.setDriver('Lee Boonstra'); 
alert(taxi.getDriver());
```
You can even use magic getter and setter methods to access complex objects. For example, let’s change the code in Example 4-2 and define a config with a complex object:

```
config: { 
	driver: {		firstName: "John",
		lastName: "Doe" 
	}}
```
I can get access to its properties with the line `taxi.getDriver().firstName`.
The config object in a class definition is very useful for class instances. Sometimes you don’t want to instantiate a class; for example, you may just want to run some default common utility functions. Singletons and static members would do the trick. We’ll discuss them in the next section.

#### Defining Singletons and Static Members
It is pretty simple to define a class as a singleton — just set the config singleton to true:

```
Ext.define('Utils.common.Functions', { 
	singleton: true,	//key value pairs here});
```
A singleton class definition can’t create objects (technically it can’t create more than one object, because the singleton itself gets instantiated once), but you can get access to all the methods and properties in the class itself. This is very handy for when you want to get access to generic functions or properties used as constants:

```
Ext.define('Utils.common.Logger', { 
	singleton: true,	version: "1.02", 	log: function(msg) {		console.log(msg); 	}});
```
You can call the log() function by invoking `Utils.common.Logger.log()` directly from the class.
Singletons also can contain config objects, and therefore generate magic getters and setters from properties. For example:
```
Ext.define('Utils.common.Version', { 
	singleton: true,	config: {		version: "1.03",	} });```
The previous code will generate a getter: getVersion(). Now, from anywhere in my application I can get access to this property with
```
Utils.common.Version.getVersion()```
A nice alternative for singletons are classes with a statics object defined. To set up a class with a statics object, you only need to define a statics object with key/value pairs.(Note that you can’t set a config object within a statics object)
Here we define statics with the VehicleApp.utils.Commons class:

```
Ext.define('VehicleApp.utils.Commons', { 
	statics: {		YELP_API: 'http://api.yelp.com/business_review_search?', 		YELP_KEY: 'ftPpQUCgfSA3yV98-uJn9g',		YELP_TERM: 'Taxi',		LOCATION: 'Amsterdam NL',		getUrl: function() {			return this.YELP_API + "term=" + this.YELP_TERM +				"&ywsid=" + this.YELP_KEY				+ "&location=" + this.LOCATION;		}, 	}});
```
You can create objects of a class that has statics defined, but these objects cannot get access to its properties and methods without invoking it from the class itself with the dot notation. In other words, requesting properties and methods from an instance via this will not work, but calling the full namespace (i.e., VehicleApp.utils.Commons.LOCATION) will:

```
var mySettings = Ext.create('VehicleApp.utils.Commons');//It is possible to create an instance of a class with static members: console.log(mySettings);//But getting access to a static member from an object fails: mySettings.getUrl();//Uncaught TypeError: Object [object Object] has no method 'getUrl'
```
#### Inherit from a Single Class
To create single class inheritance, you extend from a parent class by setting the extend config:

```
Ext.define('AppName.packagename.ClassName', { 
	extend: 'AppName.packagename.SomeClassName'});
// Example 4-3. Define a parent class
Ext.define('VehicleApp.vehicle.Vehicle', { 
	unit: "mph",	drive: function(speed) {		console.log(this.$className + ": Vrrroom: " + speed + " " + this.unit);	} });
// Example 4-4. Define a child class and implement inheritance
Ext.define('VehicleApp.vehicle.Car', { 
	extend: 'VehicleApp.vehicle.Vehicle', drive: function(speed) {		console.log(this.$className + ": Vrrroom, vrrroom: " + speed + this.unit); 	}});

var vehicle = Ext.create("VehicleApp.vehicle.Vehicle");vehicle.drive(40); //alerts "Vrrroom: 40 mph" 
var car = Ext.create("VehicleApp.vehicle.Car");car.drive(60); //alerts "Vrrroom, vrrroom: 60 mph"
```
As with any object-oriented language, if you need to do further initializations upon creation, you code a constructor. It makes sense to code an initConfig(config) method in a constructor.

The `initConfig(config)` method initializes the configuration for this particular class. Whether you initialize default config values or pass in config values as an argument while creating an object, this method will override and merge them all together and create an instance with these default settings.When you are inheriting from other classes, you don’t need to rewrite the initConfig method. It’s inherited, so the functionality is already there, but it does need to exist. Typically, the best place to include it would be in your base class.

Another powerful method is `callParent([arguments])`. It also makes sense to write this call in the constructor, although you don’t have to. You can run this from any other method, as shown in Example 4-6, which I will discuss shortly.It’s important to have this call in your `custom classes`, because you always want to initialize the config settings from every parent. 
You are free to call the parent from whatever method you are in. This can be handy for overriding functionality.In Example 4-6, I want to override the drive() function that is inherited from the Vehicle class in order to customize it specifically for a `Motor` class

```
// Example 4-6. Class inheritance
Ext.define('VehicleApp.vehicle.Motor', {	extend: 'VehicleApp.vehicle.Vehicle',	config: { 		nrOfWheels: 2 //	},	constructor: function (config) { 		this.initConfig(config); //	},	drive: function(speed) { //		if(this.getNrOfWheels() < 3) { // 		console.log(this.$className +			": Vrrroom, vrrroom on " + this.getNrOfWheels() + " wheels.");		} else { 			this.callParent([60]); //		} 	}});
```
注意以上的constructor部分，最后把这部分放到base class( 即Vehicle.js),放这里只做demo之用
I can run this code by creating an instance of the Motor class.```
var motor = Ext.create('VehicleApp.vehicle.Motor', { 
//nrOfWheels: 4}); motor.drive();```
Constructors aren’t used with components. If you subclass Ext.Component, you probably won’t use a constructor. Instead, components are initialized in a method named `initialize()`.
Inheritance is a very powerful concept. What about multiple inheritance?
#### Inherit from Multiple Classes

You might want a class to inherit features from more than one superclass. When you want to implement multiple inheritance of classes, you need `mixins`,A mixin object does exactly what the name implies: it mixes in functionality.“Take a little bit of this, use a little bit of that...” For example, take a method of class X, take a method of class Y, implement it in class Z.
Let’s say we have two vehicle classes, a normal car and a monster 4-wheeler. Both vehicles can inherit the methods to brake and to drive. Only the monster 4-wheeler can also jump, however; the normal car can’t.
{% img /images/2014/10/multi-inherit.png%}
Now, let’s define three classes, each with its own functionality to share drive(), brake(), and jump(). Later you will define two vehicle classes that inherit from these classes and mix in those methods.
```
// Example 4-7. Define classes with the methods you will mix in

Ext.define('VehicleApp.mixins.Drive', { 
	drive: function(){ //the method to share	console.log(this.$className + ": Vrrrrooom"); }}); 
Ext.define('VehicleApp.mixins.Brake', {	brake: function(){ 		console.log(this.$className + ": Eeeeekk");	}
});
Ext.define('VehicleApp.mixins.Jump', {	jump: function(){ 		console.log(this.$className + ": Bump");	}	 });	```
Finally, you can define the two Vehicle classes with the mixin implementations. Again, these are just normal class definitions, but with a mixins object. You can list all the mixins underneath one another
```
// Example 4-8. Define a Car class with mixin implementations
Ext.define('VehicleApp.vehicle.Car', { 
	mixins: {		canBrake: 'VehicleApp.mixins.Brake',		canDrive: 'VehicleApp.mixins.Drive' 		}});
// Example 4-9. Define a FourWheeler class with mixin implementations
Ext.define('VehicleApp.vehicle.FourWheeler', { 
	mixins: {		canBrake: 'VehicleApp.mixins.Brake', 		canDrive: 'VehicleApp.mixins.Drive', 		canJump: 'VehicleApp.mixins.Jump'	} 
});```With the implementation of Examples 4-8 and 4-9, the drive() and brake() methods are available to the Car class, and the drive(), brake(), and jump() methods are available to the FourWheeler class.
```
var mercedes = Ext.create('VehicleApp.vehicle.Car');var honda = Ext.create('VehicleApp.vehicle.FourWheeler');mercedes.drive(); mercedes.brake();honda.drive(); honda.jump(); honda.brake();```
The mixin identifier `canBrake` matches the prototype, and therefore you can run the brake() method on the FourWheeler and Car classes.
#### Summary
Vanilla JavaScript by its nature has no class system, as JavaScript is a prototype-based language. To mimic the ideas of object-oriented programming, you can write your JavaScript functions in an object-oriented way:```
function Cab(driver, passenger) { 
	this.driver = driver; 
	this.passenger = passenger;}```
To create an instance of this Cab class, you can create a new object with the new operator:
```
var mercedes = new Cab('John Doe', 'The president');```
Sencha Touch has a built-in class system that ships with inheritance, magic methods, and singleton strategies. Example 4-10 is the Sencha version of the previous Cab class.

```
//Example 4-10. app/view/Cab.js
Ext.define('TaxiApp.view.Cab', { 
	extend: 'Ext.Component', 
	config: {		driver: '',		passenger: '' 	}});
```
To create an instance of this Sencha Cab class, you can create a new object, but without the new operator:

```
var mercedes = Ext.create('TaxiApp.view.Cab', { 
	driver: 'John Doe',	passenger: 'The president'});
```
下一章我们将学习`how to create layouts for your Sencha applications and components.`