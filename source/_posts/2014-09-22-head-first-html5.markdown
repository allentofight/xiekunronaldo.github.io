---
layout: post
title: "Head First Html5"
date: 2014-09-22 08:44:56 +0800
comments: true
categories: Html5
---
####JS
对象的操作

* 如何创建一个对象

```
var fido = {
	name: "Fido",
	weight: 40,
	breed: "Mixed",
	loves = ["walks", "fetching balls"]
}
```
* 获取对象的属性

```
fido.weight 或者 fido["weight"]
```

* 改变一个属性的值

```
fido.weight = 27;
fido.loves.push("chewing bones");
```

* 遍历一个对象的属性

```
var prop;
for (prop in fido) {
	alert("Fido has a "+prop+" property ");
	if (prop == "name") {
		alert("This is "+ fido[prop]);
	}
}
```
需要注意的是遍历时对象属性的顺序是任意的

* 遍历对象的数组

```
var likes = fido.loves;
var likesString = "Fido likes";

for (var i = 0; i < likes.length; i++) {
	likesString += " "+ likes[i];
}
alert(likesString);
```
* 可以任意添加或删除属性
添加属性

```
fido.age = 5;
```
删除属性
```
delete fido.age;
```
* 对象也能有方法(函数)

```
var fido = {
	name: "Fido",
	weight: 40,
	breed: "Mixed",
	loves = ["walks", "fetching balls"]
	bar: func() {
		alert("Woof woof!");
	}
}
```
可以像以下这样调用

```
fido.bark();
```
`注意`如果在函数中引用了对象的属性，属性前必需加this

```
var movie1 = {	title: "Plan 9 from Outer Space",	genre: "Cult Classic",	rating: 5,	showtimes: ["3:00pm", "7:00pm", "11:00pm"],	getNextShowing: function() {		var now = new Date().getTime();		for (var i = 0; i < this.showtimes.length; i++) {			var showtime = getTimeFromString(this.showtimes[i]); 
						if ((showtime - now) > 0) {				return "Next showing of " + this.title + " is " + this.showtimes[i];			} 
		} 		return null; 	}};
```
* 构造器

```
function Dog(name, breed, weight) { 
	this.name = name;	this.breed = breed; 	this.weight = weight; 	this.bark = function() {		if (this.weight > 25) { 			alert(this.name + " says Woof!");		} else {			alert(this.name + " says Yip!");		} 
	};}
```
用构造器创建对象

```
var fido = new Dog("Fido", "Mixed", 38);
```
* element object
标签p对应的对象的属性和方法

```
innerHTML
childElementCount
firstChild

appendChild
insertBefore
setAttribute
getAttribute

```
###Geolocation
To test your geolocation code on a mobile device, you’re going to want a server.
Unless you have a means of loading yourHTML, JavaScript and CSS files directly onto your mobile device, the easiest way to test them is to place them on a server,  and access them there.we encourage you to follow along with the code on your desktop, and once you have it working there, then test on your mobile device using the server 

* Some devices may support the other coordinates properties: altitude, altitudeAccuracy, heading, and speed.

###CSS

1. display: inline 和 display: inline-block的[区别](http://stackoverflow.com/questions/8969381/what-is-the-difference-between-display-inline-and-display-inline-block)

2. CSS Links

```
/* unvisited link */
a:link {
    color: #FF0000;
}

/* visited link */
a:visited {
    color: #00FF00;
}

/* mouse over link */
a:hover {
    color: #FF00FF;
}

/* selected link */
a:active {
    color: #0000FF;
}

```
但是需要注意 

* a:hover MUST come after a:link and a:visited
* a:active MUST come after a:hover

3. [margin Collapse](http://960development.com/understand-css-margins-collapsing/)

###HTML

block element: `<h1>, <p>, <ul>, <table>`前后会留间隙

`div`block container

inline element: `<b>, <td>, <a>, <img>`

### 工具
* Modernizr

	detect support

```
	if (Modernizr.geolocation) { 
		console.log("You have geo!");	}	if (Modernizr.localstorage) {		console.log("You have web storage!"); }	if (Modernizr.video) { 		console.log("You have video!");	}
```
