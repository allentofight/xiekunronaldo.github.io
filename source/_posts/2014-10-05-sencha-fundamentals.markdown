---
layout: post
title: "Sencha Fundamentals"
date: 2014-10-05 15:25:28 +0800
comments: true
categories: Sencha Touch
---
All of the Sencha Touch visual components (like panels, lists, and toolbars) extend from the Ext.Component class. This base class gives every component the ability to set certain standard layout-related properties, such as height, width, margin, padding, and style, or set content with the html or template (tpl) configs. Every component in Sencha Touch can be rendered to the DOM.

本章中我们将学习以下几点

* How to instantiate a basic component* How to implement templates* How to make references to components* How component traversing works* How to make references to DOM nodes* How to fire and remove events using event handling### How to instantiate a basic component
创建一个Sencha Touch view component
```
var c = Ext.create('Ext.Component', { 
	//key value pairs here, i.e.	html: 'Hello World!'});```in Sencha Touch you use a method, Ext.create(), to create a new object instance (in this case, Ext.component). This is because Sencha Touch has a built-in class system and class loader.
`Note`:在js中，创建一个对象用new，但在sencha touch中,几乎不用，because the new operator won’t inform the Sencha loader to load the required classes in the background
A much simpler way to render components to the DOM is what we call object literal notation (or lazy instantiation). You will probably use this more often when building real-world applications:
```
var c = {	xtype: 'component', html : 'Hello World'}```
什么是xtype?xtype是以下键值对的别名`alias: widget.<some-component>`,通常你在以下两种情况下用到xtype:

* When you define your own custom class (a blueprint for objects you create), you set an alias name to this component so you can easily refer to it later.(下一章中我们将学习如何创建自己的类)* To create a component instance in the object literal notation, use the xtype defined in the class blueprint (as above).
每一个Sencha view component都有一个xtype名字
* `Ext.List`对应`xtype:'list'`
* `Ext.navigation.View`对应`xtype:'navigationview'`
Tip

```
When you use Sencha Architect for building Sencha Touch or Ext JS apps and you are creating an interface, xtype has a different name. You will need to look for the setting useralias. In the code that it generates, Sencha Architect will use alias: widget.<useralias-name>. So, in other words, xtype: <useralias-name> is equivalent to alias: widget.<useralias-name>.
```
当我们创建components时(并且你不使用sencha-touch-all.js/sencha- touch-all-debug.js frameworks)，你需要告诉Sencha的Ext.Loader将相应的类载入内存中,Ext.Loader能将所有你指定的类和信赖关系按正确的顺序加载(类似java中的import),当你定义了自己的类并且嵌入了xtypes时，你可以将需要的类放requeires数据中告诉Ext.Loader去加载这些类,当用Ext.create()来创建这些components时,你能通过Ext.require()方法来加载这些类,`Ext.require("Ext.List");` or `requires: ["Ext.List"]`
注意以上的例子中,我们把html的内容写死了,在生产环境中这是非常不合适的,我们常常希望能动态生成内容,我们改用Templates来实现

```
Ext.create('Ext.Component', {	tpl: Ext.create('Ext.XTemplate','<h1>{name}</h1><p>{description}</p>'), 	data: {		name: 'Template',		description: 'This is an example of how to configure a basic template.' 	}});
```
如上所示,Ext.Component允许你使用Ext.Xtemplate来动态生成内容，再来看一个生产环境中的例子

```
Ext.onReady(function() {	var data = {		name: 'Taxi Amsterdam',		description: 'The only taxi in Amsterdam that does not circle around'	};	Ext.create('Ext.Component', {		tpl: '<h1>{name}</h1><p>{description}</p>', data: data,		styleHtmlContent: true,		cls: 'box',		renderTo: Ext.getBody()	}); });
```
动态改变数据

```
var data = {	name: 'Taxi Amsterdam',	description: 'The only taxi in Amsterdam that does not circle around.'};var c = null;Ext.require('Ext.Component');Ext.onReady(function() {	c = Ext.create('Ext.Component', {		tpl: '<h1>{name}</h1><p>{description}</p>', 		data: data,		styleHtmlContent: true,		cls: 'box',
		renderTo: Ext.getBody() 
	});
	data.description = "We like tourists a lot!"; c.setData(data);});
```
以上的tpl还可简化为

```
var myTpl = Ext.create('Ext.XTemplate','<h1>{name}</h1><p>{description}</p>');
c = Ext.create('Ext.Component', {		tpl: myTpl, 		data: data,		styleHtmlContent: true,		cls: 'box',
		renderTo: Ext.getBody() 
	});
```
在实际生产环境中,我们会把所有的HTML的templates保存到一个custom separate (singleton) class中，而不是让它暴露在全局域中(参见第八章中的` Defining Singletons and Static Members`):

```
//Example  utils/Template.js 
Ext.define('Utils.utils.Template', {
	statics: {		MY_TPL: Ext.create('Ext.XTemplate','<h1>{name}</h1><p>{description}</p>');} });
```
#### 实现更高级的Templates
如果你不想把数据保存在一个随机的变量中，而是将数据从data store(假设为CabStore)load到template中,如下:

```
Ext.create('Ext.data.Store', { 
	id:'CabStore',	fields: ['name', 'description'], 
	data : [		{ name: "Taxi Amsterdam", description: "The best taxi service" + "of Amsterdam."},		{ name: "Cab & Co", description: "Always fast."} 	]});
var myTpl = Ext.create('Ext.XTemplate', '<tpl for=".">', 	'<div class="row">',		'<h1>{name}</h1><p>{description}</p>', 	'</div>',	'</tpl>');

Ext.create('Ext.DataView', {	itemTpl: myTpl,	styleHtmlContent: true,	cls: 'box',	fullscreen: true,	height: 250});
```

`<tpl for=".">` looping tags, which loop through the root (.) of all the data records. This `<tpl>` tag sets the template markup for every row of data.

XTemplates是很强大的，不仅限于以上功能,You can run conditional expressions, basic math functions, built-in variables, custom member functions, and loops into templates.
#### 引用Components
之前我们讨论了如何创建一个component并配合template使用,现在我们看看如何引用component
```
ComponentQuery:Ext.ComponentQuery.query('selector');
```
//注意,In the component query, you pass in a string with the xtype:

```
var cars = Ext.ComponentQuery.query('car');
```
或者搜索选择多个components:

```
var carsAndCabs = Ext.ComponentQuery.query("car, cab");
```
`Tip`

```
An alternative way of creating references to components is by using a refs object in the controller.
```
It’s also possible to retrieve components with a component query for `id` or `itemId`, by passing in the string id name, prefixed with a #. As itemIds are not globally unique, it is a better practice to use them(`itemIds`) instead of ids.```
Ext.ComponentQuery.query('#mybutton');```
You can even make your component query more complex by passing in CSS-like selectors.
select all panels that have the attribute title="Test":

```
Ext.ComponentQuery.query('panel[title="Test"]');
```
Or we can select only child elements from a component:

```
Ext.ComponentQuery.query('formpanel > button');
```
#### Traversing Components
以上最后一个例子就是`Traversing Components`,To traverse components, you can use the up() and down() methods to retrieve the first parent or first child component:

```
//get first parentmyComponent.up(el); //get first childmyComponent.down(el);

var form = myButton.up('formpanel');var input = form.down('textfield[name="lastname"]');
```
the up() or down() method returns the first parent or child that it finds.
 
```
> Ext.ComponentQuery.query('button') 
[Class, Class]

> Ext.Viewport.down('button')
Class {onInitializedListeners: Array[0],
initialConfig: Object, id: "mybutton",
getId: function, element: Class...}
```
#### Making References to DOM Nodes
when you are creating custom components and plug-ins yourself, you might need to work with the DOM.

`Note`

Creating custom components or plug-ins in Sencha Touch is beyond the scope of this book. It’s an advanced technique that requires an understanding of the Sencha class system, DOM, and custom styling. If you are interested in custom components, take a look at the [Sencha Market](http://market.sencha.com/), where a lot of plug-in developers share their components and plug-ins for free.

Sencha提供了三种方式获取DOM

* Getting Sencha’s version of a DOM element.
* Getting a collection of multiple direct DOM elements. * Getting the direct DOM node.
假设有以下代码

```
	<h2 id="title">Taxi Amsterdam</h2> 
	<div id="description">		<p>Taxi description</p>		<p>Taxi description2</p> 	</div>
```
##### Exg.get()
Ext.get(el) is the trick you use to retrieve the Ext.dom.Element, which is a kind of wrapper that encapsulates a DOM element, adding simple DOM manipulation facilities and normalizing for browser differences.
你可以传入以下三种形式

* An ID to the node* A DOM node* An existing Ext.Element
测试结果

```
var title = Ext.get('title'); 
console.log(title);
//result
Class {dom: h2#title, id: "title", self: function, 
superclass: Object, defaultConfig: emptyFn}
```
`Note`

```
	Ext.get() is the shorthand notation for Ext.dom.Element.get().

```
##### Ext.select()
选择多个DOM elements,我们可以用Ext.select(selector)
You can pass in a CSS-like selector (with an xtype, itemId, or CSS class), and it returns an Ext.dom.CompositeElementLite, which is a collection of DOM elements providing methods to filter members or to perform actions upon the whole set.

```
> var pars = Ext.select("p");Class {elements: Array[2], el: Class, self: function, superclass: Object, defaultConfig: emptyFn}
```
获取第一个DOM Node

```
	> var firstPar = Ext.select("p").elements[0]; 
	<p>Number one taxi in Amsterdam.</p>
```

`Note`

在JS中,你用以下方法来获取一个tag name的所有DOM

```var pars = document.getElementsByTagName('p');
```
##### Ext.getDom()
Finally, you can also request the true DOM with Ext JS by using Ext.getDom(el).
You can pass in the following:

* An ID to the node* A DOM node* An existing Ext.Element
```
	> var title = Ext.getDom('title'); 
	<h2 id="title">Taxi Amsterdam</h2>```
既然我们已经知道如何获取DOM elements和Sencha components,我们可以看看如何do something with these references
##### Handling Events
Event handling is done via the Ext.util.Observable mixin,A mixin is a technique to support (multiple) inheritance.(第四章将有介绍)The Ext.Component class uses the Ext.util.Observable mixin, which means that all components are able to respond to events, because Ext.Component is the base class for every view component in Sencha Touch.

有三种类型的events

* System events

	Events invoked by the framework — for example, loading data with the load event.
* Lifecycle events

	Events invoked by the framework lifecycle — for example, painting a view with the painted event.
* User events

	Events invoked by the user — for example, a tap event.
本节我们将关注User events

###### Firing Events
在原生的JS代码中，如果你想监听一个tap event

```
function myEventHandler(ev) { //do something}element.addEventListener('touchend', myEventHandler);
```
在Sencha Touch中，你可以按如下方式来定义

```
listeners : {	tap: 'myEventHandler'}
```
来看Sench Touch中的3种监听方式

* listeners

	the listeners configuration directly into the component class:

```
Ext.define('BookTaxiBtn', { 
	extend: 'Ext.Button', 
	xtype: 'booktaxibtn', 
	config: {		text: 'Book a Taxi - listeners', margin: 5,		listeners: {			tap: 'bookTaxiEventHandler' 		},	},	bookTaxiEventHandler: function(b){		console.log('You tapped the ' + b.getText() + 'button'); 	}});
```
* handler

```
	var callTaxiBtn1 = Ext.create('Ext.Button', { 
		text: '1: Call a Taxi - handler',		margin: 5,		handler: callTaxiEventHandler	});```
* addListener()
```
var callTaxiBtn2 = Ext.create('Ext.Button', { 
	margin: 5,	text: '2: Call a Taxi - addListener' });
callTaxiBtn2.addListener('tap', callTaxiEventHandler);```
Here we use the short version of the addListener() method: the on() method. It takesthe event name and the function-to-execute name as arguments:
```
var callTaxiBtn3 = Ext.create('Ext.Button', { 
	margin: 5,	text: '3: Call a Taxi - on' });callTaxiBtn3.on('tap', callTaxiEventHandler);```
You’ll just need to wrap each button in an Ext.application.launch() method:

```
	Ext.application({ 
		name: 'Events',		requires: ['BookTaxiBtn'], 		launch: function() {			var callTaxiEventHandler = function(b) {				console.log('You tapped the ' + b.getText() + 'button');			};			var callTaxiBtn1 = Ext.create('Ext.Button', { 				text: '1: Call a Taxi - handler',				margin: 5,				handler: callTaxiEventHandler			});			var callTaxiBtn2 = Ext.create('Ext.Button', { 				margin: 5,				text: '2: Call a Taxi - addListener' 			});			callTaxiBtn2.addListener('tap', callTaxiEventHandler);
				var callTaxiBtn3 = Ext.create('Ext.Button', { 				margin: 5,				text: '3: Call a Taxi - on' 			});
				callTaxiBtn3.on('tap', callTaxiEventHandler);			//Display the buttons, for testing purposes			Ext.create('Ext.Container', { fullscreen: true, 			padding: 10,			items: [				callTaxiBtn1, 				callTaxiBtn2, 				callTaxiBtn3,				{ xtype: 'booktaxibtn'}			] 			});		} 	});
```
###### Removing Events
To remove a listener, you need a reference to its function. In case you want to remove event listeners, it’s a good practice to not write event-handler function bodies inline in the code. You should always assign functions to a variable;otherwise, it will be hard to reference the function to remove(from outside the function body).
原生的JS，可以按以下方式来removeEventListener

```
element.removeEventListener('touchend',myEventHandler,false)
```
To remove an event listener, use the removeListener() method, pass in the event name you want to remove

```
button.removeListener('tap', myEventHandler);
```
以下为完整演示demo

```
Ext.application({ name: 'Events',	launch: function() {		var callTaxiEventHandler = function(b) {			console.log('You tapped the ' + b.getText() + 'button'); 			this.removeListener('tap', callTaxiEventHandler); 			console.log('From now on, you can not call again.');		};		var callTaxi = Ext.create('Ext.Button', { 			text: 'Call the Taxi',			margin: 5,			listeners: {				tap: callTaxiEventHandler 
			}		});		//just for testing purposes		Ext.create('Ext.Container', { 
			fullscreen: true, 			padding: 10,			items: [callTaxi]		});	} });
```
`Note`


Just as the on() method is a shorthand version for addListener(), there is a shorthand version for the removeListener() function un(). So, instead of:

```
	button.removeListener('tap', myEventHandler);
```
you can use:

```
	button.un('tap', myEventHandler);
```	
###### Firing Custom Events
监听模式

```
listeners : {	tap: function(e){		this.up('taxiview').fireEvent('calltaxi', e); 	}}
```
The controller listens to the custom calltaxi event:

```
Ext.define('TaxiController', {	extend: 'Ext.app.Controller',	config: { control: {		'taxiview': {			calltaxi: 'onCallTaxi'		} 
	}	},	onCallTaxi: function() {		console.log("Call the Taxi"); 	}});
```