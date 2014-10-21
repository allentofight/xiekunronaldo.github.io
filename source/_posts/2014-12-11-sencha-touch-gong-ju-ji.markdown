---
layout: post
title: "Sencha Touch 工具集"
date: 2014-12-11 23:41:50 +0800
comments: true
categories: Sencha Touch
---
## Ext
* emptyFn

	Ext.emptyFn contains a reusable, empty function that you can pass as a default param‐ eter to callbacks or event handlers.

* version

	Ext.version returns the version of the current Sencha Touch library loaded in the browser.

* bind()

	Ext.bind() is used to attach functions to objects, setting the correct this pointer at the end of the operation. This function is required to solve some quirks inherent to JavaScript.

* clone()

	Ext.clone() can be used to create a shallow copy of the object passed as parameter.

* widget()

	 Ext.widget() can be used to create in‐ stances just by using the xtype instead of the whole class name.

* decode() and encode()

	Ext.decode() is a shortcut to Ext.JSON.decode(), used to parse a JSON string and return the corresponding object. Similarly, Ext.encode() is a shorthand for Ext.JSON.encode() and returns the JSON string that represents the object passed as parameter.

* defer()
	
	Ext.defer() is a shortcut for Ext.Function.defer(), used to delay the execution of a function by some milliseconds. The function also takes as parameters the context of execution, some optional arguments, and of course the number of milliseconds before executing the function. For more control over delayed execution, check the Ext.DelayedTask class, which provides tighter control and more options.

* destroy()
	Ext.destroy() is used to remove objects from memory completely, avoiding memory leaks or dangling references. The objects passed as parameters of this function will be removed from the DOM, and their event listeners will be removed as well.
* each()
	Ext.each() is a shortcut to Ext.Array.each(), and as the name implies, it takes an array as both parameter and function, and executes the function passing each element of the array as parameter. The callback function can be return false at any time to stop the iteration process.
* getBody(), getDoc(), and getHead()
	These three functions return a pointer to the current HTML elements representing the <body>, <html>, and <head> elements, respectively.

* getClass() and getClassName()

	These functions can be used to query any Sencha Touch object about itsclass:

```javascript
	
	var button = Ext.create('Ext.Button'); 

	var classObject = Ext.getClass(button); 

	var className = Ext.getClassName(button);

```

The output of the Ext.getClass() function is a complex object dump, showing the complete internal structure of class objects:

```javascript
$className: "Ext.Button"
$isClass: true
$onExtended: Array[2]
addConfig: function (config, fullMerge) {
addInheritableStatics: function (members) {
addMember: function (name, member) {
addMembers: function (members) {
addStatics: function (members) {
addXtype: function (xtype) {
arguments: null
borrow: function (fromClass, members) {
callParent: function (args) {
caller: null
create: function () {
createAlias: function (a, b) {
displayName: "Ext.Button"
extend: function (parent) {
getName: function () {
implement: function () {
length: 0
mixin: function (name, mixinClass) {
name: ""
onExtended: function (fn, scope) {
override: function (members) {
prototype: Object
superclass: Object
triggerExtended: function () {
xtype: "button"
__proto__: function Empty() {}		
```

* getCmp() and getStore()
	These functions return pointers to specific Sencha Touch objects; getCmp() takes an ID as a parameter and returns a pointer to the corresponding component (not HTML element, but `component`!). On the other hand, getStore() is a shortcut to Ext.da ta.StoreManager.lookup() and provides a handy mechanism to get a pointer to any store defined in the current application.
* id()
	The id() function is used to create unique ID values for individual components.
* isArray(), isBoolean(), isDate(), isDefined(), isElement(), and others

```javascript
var array = [];
var bool = Ext.isArray(array);
```

* iterate()

```javascript
var obj = {
key1: 'value1',
key2: 'value2', key3: 345, key4: false
};
Ext.iterate(obj, function (key, value) {
var str = key + ': ' + value;
console.log(str); }, this);
var arr = [ 'value1', 'value2', 678, true ]; var index = 0;
Ext.iterate(arr, function (item) {
var str = index + '> ' + item;
console.log(str);
++index; }, this);
```

* typeOf()

Similarly to the isArray() family of functions previously described, the typeOf() function works as an extended (and patched) alternative to the typeof() function provided by the JavaScript standard. The following code shows an example of how to use it:

```javascript
var undef;
var nil = null;
var str = 'string';
var num = 234;
var bool = true;
var date = new Date(); 
var func = function () {
	console.log('something');
};
var obj = { key1: 'value1' };
var arr = [ 'value1', 'value2' ]; 
var reg = /match/gi;
var elem = Ext.getDoc().dom;
var all = [ undef, nil, str, num, bool, date, func, obj, arr, reg, elem ];
var index = 0;
Ext.iterate(all, function (item) {
	var txt = index + '> ' + item + ': ' + Ext.typeOf(item); 
	console.log(txt);
	++index;
}, this);
```
The result of the execution of this code is the following:

```javascript
 0> undefined: undefined
    1> null: null
    2> string: string
    3> 234: number
    4> true: boolean
    5> Wed Nov 07 2012 14:24:22 GMT+0100 (CET): date
    6> function () {
        console.log('something');
    }: function
    7> [object Object]: object
    8> value1,value2: array
    9> /match/gi: regexp
    10> [object HTMLHtmlElement]: element
```
### Ext.Array


* clean()

The clean() function removes “empty” items from an array. The mentioned earlier empty in this case is brought by the Ext.isEmpty() function, described later in this chapter.

```javascript
var sampleArray = [1234, true, 'asdfadf', null, 'another string', '', 0]; 
var cleanArray = Ext.Array.clean(sampleArray);
```
The preceding code yields an array that only contains the values [ 1234, true, 'asd fadf', 'another string', 0 ].

* contains()

The contains() function returns a Boolean value stating whether a certain value exists in the array or not.

```javascript
var sampleArray = [1234, true, 'asdfadf', null, 'another string', '', 0]; 
var tests = [false, '', 'whatever', 'asdfadf', 1234, 6578];
Ext.Array.each(tests, function (item) {
if (Ext.Array.contains(sampleArray, item)) {
            console.log('"' + item + '" IS contained');
        }
else {
	console.log('"' + item + '" is NOT contained');
}
}, this);
```
The result of the code is the following:

```javascript
"false" is NOT contained
    "" IS contained
    "whatever" is NOT contained
    "asdfadf" IS contained
    "1234" IS contained
    "6578" is NOT contained
```

* difference()
The difference() function returns a new array containing the result of subtracting, from the first array passed as parameter, the elements that exist in a second array passed as parameter. For example:

```javascript
var sampleArray = [1234, true, 'asdfadf', null, 'another string', '', 0]; 
var anotherArray = ['asdfadf', new Date(), 1234, true, '', 234];
var difference = Ext.Array.difference(sampleArray, anotherArray);
```
The contents of the preceding difference variable are the following: [ null, 'another string', 0 ].

* erase()

The erase() function provides a simpler API to the standard Array.splice() method of the JavaScript standard library. It allows you to remove a certain number of items from an array, starting at a particular point.

```javascript
var sampleArray = [1234, true, 'asdfadf', null, 'another string', '', 0]; 
var erasedArray = Ext.Array.erase(sampleArray, 1, 3);
```

* every() and some()

The every() and some() functions iterate over the items of an array, evaluating each of them in a function passed as parameter; both functions work very similarly, with the difference that every() stops iterating as soon as the function returns false, while some() will stop iterating as soon as the function returns true. For example:

```javascript
var sampleArray = [1234, true, 'asdfadf', null, 'another string', '', 0]; 
var result = Ext.Array.every(sampleArray, function (item) {
        // If here the inner function returns "false" at any point, the
        // loop ends and the value in "result" will be "false."
        // In this case, however, as 'whatever' is not contained in the
        // sampleArray, the function always returns true, and 'result'
        // contains true as well.
return (item !== 'whatever'); }, this);
    console.log('Result of `every()`: ' + result);
var sampleArray = [1234, true, 'asdfadf', null, 'another string', '', 0]; 
console.log('Original Array');
console.dir(sampleArray);
var result = Ext.Array.some(sampleArray, function (item) {
// If here the inner function returns "true" at any point, the
// loop ends and the value in "result" will be "true."
// In this case, however, as 'another string' is contained in the
// sampleArray, the function returns true at some point, and 'result' // contains true as well.
return (item === 'another string');
}, this);
console.log('Result of some(): ' + result);
```
The respective outputs would look like this:

```javascript
Result of every(): true
Result of some(): true

```

* filter()

    The filter() function evaluates every item of the array passed as parameter, and it returns a new array that contains all the values where the provided function returns true.

```javascript
var sampleArray = [1234, true, 'asdfadf', null, 'another string', '', 0]; 
var filteredArray = Ext.Array.filter(sampleArray, function (item) {
return (typeof(item) === 'string'); }, this);
```

* flatten()

    The flatten() function takes an array with nested arrays and brings all the values in the nested arrays as part of the topmost one:

```javascript
var sampleArray = [1234, true, 'asdfadf', ['inner array', true, false, 234], 'an other string', '', 0];
var flattenedArray = Ext.Array.flatten(sampleArray);
```

* from()
The from() function creates a new array from any object or primitive passed as parameter:
```javascript
var bool = true;
var booleanArray = Ext.Array.from(bool);
```

* include()
    The include() function works like a selective push() method: It adds new items to an array only if those items do not already exist, ensuring that the each element appears only once:
```javascript
var sampleArray = [1234, 456, true, 'test']; 
Ext.Array.include(sampleArray, true); 
Ext.Array.include(sampleArray, 'whatever');
```
The result of the preceding code is the following array: [ 1234, 456, true, 'test', 'whatever' ]. The value true is not added a second time, since it already exists when the first call to include() is made.

* indexOf()
    As the name implies, this function returns the index of the item passed as parameter. In case the object is not found, the function returns -1:

* insert()
The insert() function allows you to interpolate values in an existing array at a partic‐ ular position:
```javascript
var sampleArray = [1234, 456, true, 'test'];
var itemsToInsert = [768, 'something', false, null];
var insertedArray = Ext.Array.insert(sampleArray, 1, itemsToInsert);
```
The result of the preceding code is the following: [ 1234, 768, "something", false, null, 456, true, "test"]. The values of the array itemsToInsert have been em‐ bedded in the first array, starting at position number 1.

* intersect()
The intersect() function returns a new array that merges the values from all the arrays passed as parameter (this function can take a large number of arguments):
```javascript
var sampleArray = [1234, 456, true, 'test'];
var anotherArray = [true, false, 'test', 'whatever', 456];
var intersection = Ext.Array.intersect(anotherArray, sampleArray);
```
The result of the intersection is the following: [ true ].

* map() and pluck()
The map() and pluck() functions will sound familiar to developers versed in functional languages such as Haskell or Lisp. They both operate over the contents of an array but in different ways: map() will execute a function in each item, returning a new array with the results of each execution. On the other hand, pluck() will return a new array with the values of a particular key from an array of objects:
```javascript
var sampleArray = [1234, 456, true, 'test'];
var mapped = Ext.Array.map(sampleArray, function (item) {
return item + '_boom'; }, this);
```

The mapped array looks like this: [ '1234_boom', '456_boom', 'true_boom', 'test_boom' ].
```javascript
var sampleArray = [{ key1: 'value1',
        key2: 'value2'
        }, {
        key1: 'value3',
        key2: 'value4'
    }, {
        key1: 'value5',
        key2: 'value6'
    }, {
        key1: 'value7',
        key2: 'value8'
    }, {
        key1: 'value9',
        key2: 'value0'
    }];
console.log('Original Array'); console.dir(sampleArray);
var plucked = Ext.Array.pluck(sampleArray, 'key1');
```

After this code executes, plucked contains all the values referenced by the key key1: [ 'value1', 'value3', 'value5', 'value7', 'value9' ].

* max(), mean(), min(), and sum()

```javascript
var sampleArray = [1234, 456, -234, 654, 0, 'whatever']; var max = Ext.Array.max(sampleArray);
var min = Ext.Array.min(sampleArray);
var sum = Ext.Array.sum(sampleArray);
var sampleArray2 = [1234, 456, -234, 654, 0]; var mean = Ext.Array.mean(sampleArray2);
```
max(), mean(), min(), and sum()
All of these functions perform the typical mathematical operations on any array:
```javascript
var sampleArray = [1234, 456, -234, 654, 0, 'whatever']; var max = Ext.Array.max(sampleArray);
var min = Ext.Array.min(sampleArray);
var sum = Ext.Array.sum(sampleArray);
var sampleArray2 = [1234, 456, -234, 654, 0]; var mean = Ext.Array.mean(sampleArray2);
```
After executing the preceding code, max is 1234, min is equal to -234, sum is 2110what ever (pay attention to how the string was added at the end of the mathematical sum!) and mean is equal to 422.

* merge() and union()
The merge() function (and its alias union()) create a new array with all the values of those passed as parameter, but appearing only once:
```javascript
var sampleArray = [1234, 456, true, 'test'];
var anotherArray = [true, false, 'test', 'whatever', 456]; 
var merge = Ext.Array.merge(anotherArray, sampleArray);
```
The result of the preceding code is the following: [ true, false, 'test', 'whatev er', 456, 1234 ].

* remove()
The remove() function, as the name implies, removes items from an array, but only if they exist (of course!):
```javascript
var sampleArray = [1234, 456, -234, 654, 0, 'whatever', "BOOM", true, 'test']; Ext.Array.remove(sampleArray, 'whatever');
Ext.Array.remove(sampleArray, -234);
Ext.Array.remove(sampleArray, true);
Ext.Array.remove(sampleArray, 'non existent value');
The result of the code is that sampleArray now contains the values [ 1234, 456, 654,
'0', 'BOOM', 'test' ].
```

* replace() and splice()
The replace() function takes two arrays as parameters and replaces values in the first array using the values in the second. For that it also requires some indexing information, in particular the location where to start making replacements in the first array, and the number of such replacements to make.
```javascript
var sampleArray = [1234, 456, -234, 654, 0, 'whatever', "BOOM", true, 'test']; var replacements = ['boom', 'boom again'];
Ext.Array.replace(sampleArray, 4, 2, replacements);
```
The result of the preceding code is the following: [ 1234, 456, -234, 654, "boom", "boom again", "BOOM", true, "test" ]. As you can see, the values of the replace ments array was inserted after the fifth item, replacing the values 0 and whatever.
The splice() function does exactly the same; however, it uses the same signature of the function of the same name in the JavaScript standard library:
```javascript
var sampleArray = [1234, 456, -234, 654, 0, 'whatever', "BOOM", true, 'test']; Ext.Array.splice(sampleArray, 4, 2, 'boom', 'boom again');
```
The result of this code is exactly the same as the one returned by the replace() function.

* slice()
The slice() function returns a subsection of an existing array, and it takes both the start and end indexes.
`warning`
    A very common error when using this function is to consider the second argument to be the length of the requested slice; it is not a length, it is the ending index!
```javascript
var sampleArray = [1234, 456, -234, 654, 0, 'whatever', "BOOM", true, 'test']; var slice = Ext.Array.slice(sampleArray, 4, 7);
```

* sort()
The sort() function takes an array and a comparison function as parameter and returns a new array with the same items as the original one, but sorted:
```javascript
var sampleArray = [1234, true, 'ASDFADF', -76, '', null, 'another string', '', 0];
var sorted = Ext.Array.sort(sampleArray, function (item1, item2) {
if (item1 === item2) { return 0;
}
return (item1 < item2) ? -1 : 1; });
```
The result of the code is the following: [ -76, "", null, "", 0, true, 1234, "ASD FADF", "another string"].

* toArray()
The toArray() function transforms an “iterable object” into a real array. This includes the arguments object (automatic object containing all the arguments of a function in JavaScript) as well as any literal object whose keys are numeric:

```javascript
var iterable = { 
    0: 'value0', 
    1: 'value1', 
    2: 'value2', 
    3: 'value3', 
    4: 'value4',
    length: 5 
};
var array = Ext.Array.toArray(iterable);
```
The preceding code yields an array with these values: [ 'value0', 'value1', 'value2', 'value3', 'value4' ].

* unique()
The unique() function returns a new array with only one instance of each repeated object in the original array:

```javascript
var sampleArray = [1234, 456, 'whatever', 'whatever', 456, 'whatever']; 
var unique = Ext.Array.unique(sampleArray);
```
The result of this function call is the following: [ 1234, 456, 'whatever' ].
# Ext.ComponentQuery
The Ext.ComponentQuery singleton object provides a very useful query() function, used to retrieve pointers to one or many individual components in the application using a syntax similar to that of CSS selectors. This functionality is heavily used in Sencha Touch controllers

`warning`
    Although very similar to Ext.DomQuery, the Ext.Component Query.query() function does not return pointers to DOM elements, but rather Sencha Touch components, themselves represented by one or many DOM elements. The results of Ext.ComponentQuery are then higher in the abstraction ladder.
* Component Query Syntax
The component query syntax is very similar to that of CSS selectors; to retrieve com‐ ponents, you can use the following options:
    * The xtype property.
    * The itemId property, using the #myItemId syntax (just like in CSS).
    * Attributes wrapped in brackets, such as button[action=saveFile].
    * Functions returning “truthy” values, using curly brackets: {isHidden()}.
    * Optionally, you can use the > sign to indicate parent/child relationships, or the comma (,) to concatenate different queries in one: panel > button, or button, segmentedbutton are some examples.
    * Finally,thenotoperatorcanalsobeused:not buttonwillreturnallthecomponents that are not buttons in the application.
# Ext.DateExtras
Sencha Touch includes a series of utility methods for managing `date` and `time` infor‐ mation contained within the Ext.DateExtras object. These functions all work with the standard `Date` class provided by the standard JavaScript library.
Similarly to the Ext.Array functions, many of these functions do not exist as instance methods of the Date class, but rather as static methods of the Ext.Date object.
`warning`
    Pay attention to the fact that these functions are not included by default by Sencha Touch; to use them in your code, you have to include the following line in the controller or view where you wish to use them: requires: 'Ext.DateExtras'. However, to use them, you have to use the Ext.Date object.
* add()
The add() function allows you to increment or decrement a given date by a certain interval. The type of interval can be specified using the constants available in the Ext.Date object, such as DAY, HOUR, or MONTH.

```javascript
var sampleDate = new Date();
var futureDate = Ext.Date.add(sampleDate, Ext.Date.DAY, 10);
```
The result of this code is a date 10 days in the future.

* between()
The between() function takes three dates and evaluates whether the first one is located amid the other two dates passed as parameters:

```javascript
var sampleDate = new Date();
var anotherDate = Ext.Date.add(sampleDate, Ext.Date.YEAR, 10);
var betweenDate = Ext.Date.add(sampleDate, Ext.Date.MONTH, 5);
var inBetween = Ext.Date.between(betweenDate, sampleDate, anotherDate);
```
* clearTime()
This useful function removes any time information from the date passed as parameter.

```javascript
var sampleDate = new Date();
var noTime = Ext.Date.clearTime(sampleDate);
```
* getDayOfYear(), getWeekOfYear(), isLeapYear()

```javascript
var sampleDate = new Date();
var dayOfYear = Ext.Date.getDayOfYear(sampleDate); var week = Ext.Date.getWeekOfYear(sampleDate);
var isLeap = Ext.Date.isLeapYear(sampleDate);
```
* getDaysInMonth(), getFirstDateOfMonth(), getFirstDayOfMonth(), getLastDateOfMonth(), getLastDayOfMonth()

```javascript
var sampleDate = new Date();
var daysInMonth = Ext.Date.getDaysInMonth(sampleDate);
var firstDate = Ext.Date.getFirstDateOfMonth(sampleDate); var firstDay = Ext.Date.getFirstDayOfMonth(sampleDate); var firstDayName = Ext.Date.dayNames[firstDay];
var lastDate = Ext.Date.getLastDateOfMonth(sampleDate); var lastDay = Ext.Date.getLastDayOfMonth(sampleDate); var lastDayName = Ext.Date.dayNames[lastDay];
```
* getElapsed()
The getElapsed() function returns the time interval in milliseconds between two dates passed as parameter:
```javascript
var sampleDate = new Date();
var anotherDate = Ext.Date.add(sampleDate, Ext.Date.YEAR, 10); var elapsed = Ext.Date.getElapsed(sampleDate, anotherDate);
```
The preceding code returns the value 315532800000, which is the number of millisec‐ onds contained in a 10-year period.

* getGMTOffset(), getTimezone(), isDST()
These functions provide information about the time zone of the date instance passed as a parameter:

```javascript
var sampleDate = new Date();
var offset = Ext.Date.getGMTOffset(sampleDate); 
var timezone = Ext.Date.getTimezone(sampleDate); 
var isDST = Ext.Date.isDST(sampleDate);
```
At the moment of this writing, the values returned by these functions are as follows: +0100 for the offset, CET for the time zone, and false for the DST information.
* getMonthNumber(), getShortDayName(), getShortMonthName(), getSuffix()
These functions are useful helpers for displaying time and date information in a human- readable way:

```javascript
var number = Ext.Date.getMonthNumber("June");
var shortDayName = Ext.Date.getShortDayName(4);
var shortMonthName = Ext.Date.getShortMonthName(4);
var sampleDate = new Date();
var suffix = Ext.Date.getSuffix(sampleDate);
```
In the last case, at the moment of this writing the suffix returned is th, but of course it could be any value, such as st, nd, rd, or th.
* isValid()
The isValid() function takes at least values for year, month, and day (in that order), optionally for time information and checks whether they correspond to valid date val‐ ues. This might be handy when checking user input in forms, for example:

```javascript
var isValid1 = Ext.Date.isValid(1984, 4, 6);
var isValid2 = Ext.Date.isValid(355423, 4564564, 23424);
```
As you can easily imagine, the first set of values yields a valid date, while the second does not.
* parse()
Finally, the parse() function takes a string with date information and an optional format parameter and returns the Date instance that corresponds.

```javascript
var parsed = Ext.Date.parse('2012-11-15', 'Y-m-d');
```
## Ext.DomHelper and Ext.DomQuery
Sencha Touch also provides the required functionality for web developers to refer to the HTML elements that make up a web page, individually or as a group. For that, it exposes functions similar to those of jQuery or Zepto, taking very similar parameters and behaving in similar ways.
* select() and selectNode()
Using select() and selectNode(), developers can retrieve pointers to individual HTML objects (or groups thereof) in the current context:

```javascript
var divs = Ext.DomQuery.select('DIV');
var div = Ext.DomQuery.selectNode('#queryDiv');
```
The allowed element selectors are the following (in this example, we are using li and ul as an example, but any HTML tag can be used in this context):


`*` 
    any element
`ul, li`

    an element with any of these tags
`ul li`

    all descendant li elements of ul
`ul > li`

    all li that are direct children of ul
`ul + li`
    all li elements that are immediately preceded by ul
`ul ~ li`
    all li elements that are preceded by a sibling element with the tag ul
Developers can also use attribute selectors:
* ul[class]
    an ul element that has a class attribute

* ul[class=main]

    an ul element that has a class attribute with the main value

* ul[class$=main]

    an ul element that has a class attribute that ends with the main value

* ul[class*=main]

    an ul element that has a class attribute that contains the main value

* ul[class%=2]

    an ul element that has a class attribute whose value is evenly divisable by 2

* ul[class!=main]

    an ul element that has a class attribute that does not equal the main value
Finally, `pseudo-class` selectors are also possible: li:first-child, li:last-child, li:only-child, li:4th-child, li:odd, li:even, etc.

## append(), insertBefore(), insertAfter(), overwrite(), insertHtml()

Once you have selected one or many nodes, Ext.DomHelper provides useful functions that allow you to manipulate the HTML nodes. For example, taking the following HTML fragment:
```javascript
<div class="outerDiv"><div id="someDiv" class="innerDiv"></div></div>
```
We can operate on it as follows, and Figure 2-2 shows the result:

```javascript
var div = Ext.DomQuery.selectNode('#someDiv');
Ext.DomHelper.overwrite(div, '<div id="someDiv">This is an overwritten DIV</ div>');
Ext.DomHelper.append(div, '<p>A paragraph added using append()</p>'); 
Ext.DomHelper.insertBefore(div, '<p>A paragraph inserted before the div</p>'); Ext.DomHelper.insertAfter(div, '<p>A paragraph inserted after the div</p>');
// Valid parameters:
// beforeBegin: <HERE><div>Contents</div>
// afterBegin: <div><HERE>Contents</div>
// beforeEnd: <div>Contents<HERE></div>
// afterEnd: <div>Contents</div><HERE>
Ext.DomHelper.insertHtml('beforeBegin', div, '<div class="inserted">inserted as HTML</div>');
```

{% img /images/2014/12/Figure2-2.png%}

## Ext.env.Feature

The Ext.env.Feature class (of which Ext.feature is the singleton instance) exposes a series of properties, each highlighting a particular HTML5 feature of the browser where the application is running:

```javascript
var key = null;
var value = false;
var text = ''; console.log('Available features:'); 
for (key in Ext.feature.has) {
        value = Ext.feature.has[key];
        text = key + ': ' + value;
        console.log(text);
}
```
After executing this code in Safari 6.0.2 on OS X Mountain Lion, this is the result:

```javascript
 Available features:
    Canvas: true
    Svg: true
    Vml: true
    Touch: false
    Orientation: false
    OrientationChange: false
    DeviceMotion: false
    Geolocation: true
    SqlDatabase: true
    WebSockets: true
    Range: true
    CreateContextualFragment: true
    History: true
    CssTransforms: true
    Css3dTransforms: true
    CssAnimations: true
    CssTransitions: true
    Audio: true
    Video: true
    ClassList: true
    LocalStorage: true
```

## Ext.Function

The Ext.Function object contains useful helper...well, `functions`, that extend and aug‐ ment the already interesting native capabilities of JavaScript functions. Many of these functions are used by Sencha Touch for managing `event` handling, supporting `extended events` (such as `double-tap` events) and other features. They are also available for developers to use (and maybe abuse).

* createDelayed()

The createDelayed() function wraps a function, causes a delay in milliseconds, and returns a new function that fires the wrapped function after the requested delay:

```javascript
var fn = function (value) {
    console.log('this is fn, this is the value: "' + value + '"');
};
var delayed = Ext.Function.createDelayed(fn, 1500, this, [ 345 ]); 
delayed();
```
This function causes the text this is fn, this is the value: "345" on the console after approximately a second and a half.

* createInterceptor()
The createInterceptor() function takes a function and wraps it with another one, composing everything into a third one that is returned. When this returned function is executed, all input is piped to the wrapping function (the second argument) which must return true or false. The original function (the wrapped one, passed as first argument) is executed only if the wrapper returns true.
In the following example, originalFn() is executed only when interceptor() is called with a value of 234; the check is performed by newFn().

```javascript
var originalFn = function (value) {
console.log('original function, this is the value: "' + value + '"');
};
var newFn = function (value) {
console.log('inside newFn, value is "' + value + '"'); return (value === 234);
};
var interceptor = Ext.Function.createInterceptor(originalFn, newFn);
    interceptor(0);
    interceptor(666);
    interceptor(234);
```
The output of the code is the following:

```javascript
 inside newFn, value is "0"
    inside newFn, value is "666"
    inside newFn, value is "234"
    this is the original function, this is the value: "234"
```
his mechanism is very useful to filter input to functions, composing them as required, on the fly, at runtime.

* createSequence()
The createSequence() function is used to chain the execution of functions together; it takes two functions, and returns a new one. When the returned function is called, the wrapped functions are called, with their parameters passed accordingly. However, the return value of the wrapping function is the return value of the first function wrapped.

```javascript
var first = function (value) {
    console.log('inside the first() function, value = "' + value + '"'); 
    return 'first';
};
var second = function (value) {
    console.log('inside the second() function, value = "' + value + '"'); 
    return 'second';
};
var third = Ext.Function.createSequence(first, second);
var value = third('boom');
console.log('third() returns "' + value + '"');
```
The execution of the code yields the following result in the console:
```javascript
    inside the first() function, value = "boom"
    inside the second() function, value = "boom"
    third() returns "first"
```
* createThrottled()
The createThrottled() function is a particularly interesting one: It wraps a function and takes a delay in milliseconds as a parameter; it then filters the execution of the wrapped function, allowing it at least to wait for the specified delay before executing it again.

In the following example, the first call to throttled() executes immediately. However, in the following ones, no matter how many of them are triggered, only one will be executed, and after 4 seconds.

```javascript
var fn = function () {
console.log('This is being throttled');
};
var throttled = Ext.Function.createThrottled(fn, 4000, this);
    // The first call is executed immediately:
    throttled();
// No matter how many times we try, this will only be executed // once, and after 4 seconds:
throttled();
throttled();
throttled();
throttled();
throttled();
throttled();
```

* defer()
defer() is used to delay the execution of a function by some milliseconds. The function also takes as parameters the context of execution and, of course, the number of milli‐ seconds before executing the function.

```javascript
Ext.Function.defer(function (arg) {
console.log('This function has been deferred 1500 ms, arg = "' + arg + '"');
}, 1500, this, [ 'argument1' ]);
```
For more control over delayed execution, check the Ext.DelayedTask class, which provides tighter control and more options, including the capability of `canceling` a scheduled execution `before it happens`.

* flexSetter()

The flexSetter() function is used to provide more flexibility for passing arguments to functions. When wrapping a function using flexSetter(), it can be called using its original signature or using dictionaries of key-value pairs, allowing to chain assignations and calls in a more straightforward manner. The method wrapped by Ext.Func tion.flexSetter() must take two arguments.
For example:

```javascript
    var setValue = Ext.Function.flexSetter(function(name, value) { 
        console.log('name: "' + name + '", value: "' + value + '"');
    });
    setValue('name1', 'value1');
    setValue({
        name2: 'value2',
        name3: 'value3',
        name4: 'value4'
    });
```
The preceding code yields the following output in the console; the wrapped function by Ext.Function.flexSetter() is called as many times as required, once for each pair of name and value parameters:

```javascript
    name: "name1", value: "value1"
    name: "name2", value: "value2"
    name: "name3", value: "value3"
    name: "name4", value: "value4"
```
* pass()
The pass() function is useful to wrap a function and prepopulate the argument list, all while allowing the wrapper function to pass `new` arguments to the wrapped function.
```javascript
var originalFunction = function(){
    console.log('inside the original function, arguments:'); 
    console.dir(Ext.Array.from(arguments));
};
var callback = Ext.Function.pass(originalFunction, ['first', 'second']);
callback();
callback('third');
```
This code yields the following console messages:

```javascript
inside the original function, arguments:
Array[2]
    0: "first"
    1: "second"
inside the original function, arguments:
Array[3]
    0: "first"
    1: "second"
    2: "third"
```

## Ext.JSON
The Ext.JSON object contains an implementation of Douglas Crockford’s own JSON parser, including two functions, encode() and decode():

```javascript
var txt = '{"key1":"value1","key2":"2012-11-06T13:24:07","key3":true,"key4": 345.65,"key5":["value2","value3",768]}';
var decoded = Ext.JSON.decode(txt);
console.dir(decoded);
var obj = {
key1: 'value1',
key2: new Date(), 
key3: true,
key4: 345.65, 
key5: [
            'value2',
            'value3',
            768
] };
var encoded = Ext.JSON.encode(obj); 
console.log(encoded);
```

## Ext.Logger
The Ext.Logger object contains several useful methods that can be used to debug your Sencha Touch applications; you can think of them as extensions and complements to the usual console.log() function that we have been using in this book so far.

* deprecate(), error(), log(), and warn()

These four functions provide useful additional information to the developer when ver‐ ifying the behavior of her code on the console. They include visual hints such as icons and color to provide `more information` than the usual console.log() statement.
It could not be easier to use them:

```javascript
    Ext.Logger.deprecate('this method is deprecated');
    Ext.Logger.error('this is an error message');
    Ext.Logger.log('this is an generic log message', 0);
    Ext.Logger.warn('this is a warning message');
```
The result of the call of these methods appears in Figure 2-3.
{% img /images/2014/12/Figure2-3.png%}

## Ext.Number
The Ext.Number object contains some functions that provide useful functionality for formatting and verifying numeric input in Sencha Touch applications.
* constrain()
This function takes three parameters: a numeric value and minimum and maximum bound values. It then checks whether the first numeric value is located within the min‐ imum and maximum bounds, and if so, it returns it.

```javascript
var constrained = Ext.Number.constrain(234, 0, 6030); 
var txt = 'Constrained value: ' + constrained; console.log(txt);
```
In the preceding code, the value shown will include the constrained value 234, because it is indeed located within the provided range.

# from()
The from() function takes two values: a generic value and a default value, which must be numeric; the function then tries to parse a numeric value from the first parameter, and if this is not possible, it will return the default value provided in the second pa‐ rameter.

```javascript
var num1 = Ext.Number.from('4535', 1111);
var num2 = Ext.Number.from('Some other value', 1111); 
var txt1 = 'Retrieved value: ' + num1;
var txt2 = 'Retrieved value: ' + num2; 
console.log(txt1);
console.log(txt2);
```
In the preceding example, the output will be as follows:

```javascript
    Retrieved value: 4535
    Retrieved value: 1111
```
The first value could be parsed as a numeric input, while the second could not; hence, the default value is returned.

* snap()
This function is used to round (or “snap”) numbers, providing minimum and maximum bounds at the same time. The first parameter is the number to be rounded; the second parameter is an incremental factor, of which the result of the function will be a multiple; and the final two parameters are minimum and maximum values, used to constrain the final result:
```javascript
var num = Ext.Number.snap(223, 100, 0, 1000); 
var txt = 'Snapped value: ' + num; 
console.log(txt);
```
The preceding code will show the snapped value “200.”

* toFixed()

Finally, this function can be used to format a number as a string, using a fixed-point precision that is specified as the second parameter:

```javascript
var num = Ext.Number.toFixed(243.54234534, 2); 
var txt = 'Fixed value: ' + num; 
console.log(txt);
```

## Ext.Object

* each()

```javascript
var obj = {
key1: 'value1',
key2: 123,
key3: true };

Ext.Object.each(obj, function (key, value) { 
    var txt = [key, ': ', value].join(''); 
    console.log(txt);
}, this);
```
The console output of the preceding code is the following:
```javascript
key1: value1
key2: 123
key3: true
```

* fromQueryString() and toQueryString()
These functions perform two reverse operations, allowing the encoding and decoding of complex object structures as simple key-value pairs that could be used as URL parameters.

```javascript
var txt1 = 'key1=value1&key2=123&key3=true'; 
var obj1 = Ext.Object.fromQueryString(txt1); 
console.dir(obj1);
var obj2 = {
key1: 'value1',
key2: 123,
key3: true };
var txt2 = Ext.Object.toQueryString(obj2); 
console.log(txt2);
```
The output of the preceding code is the following:

```javascript
Object
        key1: "value1"
key2: "123"
        key3: "true"
    key1=value1&key2=123&key3=true
```

* getKey(), getKeys(), getSize(), getValues()
Predictably enough, these functions provide insight into the internal structure and contents of the object being inspected:

```javascript
var obj = {
key1: 'value1',
key2: 123,
key3: true };
var key = Ext.Object.getKey(obj, 'value1'); 
var keys = Ext.Object.getKeys(obj);
var size = Ext.Object.getSize(obj);
var values = Ext.Object.getValues(obj);
console.dir(key);
console.dir(keys);
console.dir(size);
console.dir(values);
```
The output of the code is quite self-explanatory:

```javascript
 key1
    Array[3]
        0: "key1"
        1: "key2"
        2: "key3"
        length: 3
3 Array[3]
        0: "value1"
        1: 123
        2: true
        length: 3
```
* merge()
The Ext.Object.merge() function recursively joins two distinct literal objects; an ex‐ ample will show exactly how this works:

```javascript
var obj1 = {
    key1: 'value1',
    key2: 'value2', 
    key3: 345, 
    key4: false, 
    key5: {
        innerKey1: 'innerValue1'
    }
};
var obj2 = {
    key6: 345, 
    key7: 'value1', 
    key8: 'value2', 
    key9: false, 
    key5: {
        innerKey2: 'innerValue1'
    }
};
var merged = Ext.Object.merge(obj1, obj2); 
console.dir(merged);
```
`warning`
    Ext.Object.merge() is aliased to Ext.merge() for convenience.
* toQueryObjects()

This function will encode an object into an array containing name-value pairs that can be used as parameters for sending complex nested structures as part of POST HTTP requests to a server:
```javascript
var obj = { day: 5,
        month: 3,
        year: 2002,
        time: {
hour: 9,
minute: 20 }
};
var objs = Ext.Object.toQueryObjects('date', obj, true); 
console.dir(objs);
```
The preceding code returns an array object containing the values of the original object in nested keys that can be sent as parameters for a POST HTTP request:

```javascript
 Array[5]
        0: Object
            name: "date[day]"
            value: 5
        1: Object
            name: "date[month]"
            value: 3
        2: Object
            name: "date[year]"
            value: 2002
        3: Object
            name: "date[time][hour]"
            value: 9
        4: Object
            name: "date[time][minute]"
            value: 20
        length: 5

```

## Ext.String

* capitalize()
This function takes a string as a parameter and returns the same string with its first letter capitalized:

```javascript
var sample = "whatever you shouldn't usually do."; 
var txt = Ext.String.capitalize(sample); 
console.log(txt);
```
After executing this code,the console shows the text Whatever you shouldn't usually do.

* ellipsis()
This function cuts the string passed as parameter before the number of characters specified, and adds, if required, an ellipsis at the end; for example, the following code will display the text whatever you... in the console.

```javascript
var sample = "whatever you shouldn't usually do."; 
var txt = Ext.String.ellipsis(sample, 15); 
console.log(txt);
```

* escape() and escapeRegex()
These two functions can be used to properly encode characters such as apostrophes, which cannot be properly displayed in some circumstances:

```javascript
var sample = "whatever you shouldn't usually do."; 
var txt1 = Ext.String.escape(sample);
var txt2 = Ext.String.escapeRegex(sample); 
console.log(txt1);
console.log(txt2);
```
This shows the following text in the console:

```javascript
whatever you shouldn\'t usually do.
whatever you shouldn't usually do\.
```

* format()
This function provides a very useful equivalent to the sprintf() family of functions available in many other programming languages; it takes a template string, an undefined list of parameters, and it performs the required substitutions:

```javascript
var sample = '<p class="{0}">This is a <strong>{1}</strong></p>'; 
var txt = Ext.String.format(sample, 'test', 'paragraph'); 
console.log(txt);
```

* htmlDecode() and htmlEncode()

These two functions perform inverse operations, decoding and encoding characters using their equivalent HTML entities:

```javascript
var sample1 = '&lt;p class=&quot;test&quot;&gt;
This is a &lt;strong&gt;paragraph&lt;/strong&gt;&lt;/p&gt;'; var txt1 = Ext.String.htmlDecode(sample1); 
console.log(txt1);
var sample2 = '<p class="test">This is a <strong>paragraph</strong></p>'; var txt2 = Ext.String.htmlEncode(sample2);
console.log(txt2);
```

The console would show the following:

```
 <p class="test">This is a <strong>paragraph</strong></p>
    &lt;p class=&quot;test&quot;&gt;
    This is a &lt;strong&gt;paragraph&lt;/strong&gt;&lt;/p&gt;
```

* leftPad(), repeat(), and trim()
These functions are quite self-explanatory:
```javascript
var sample1 = "whatever you shouldn't usually do."; 
var txt1 = Ext.String.leftPad(sample, 10, '-'); 
console.log(txt1);
var txt2 = Ext.String.repeat('=-=-=-=', 50, '/'); 
console.log(txt2);
var sample3 = " whatever you shouldn't usually do. "; 
var txt3 = Ext.String.trim(sample3);
console.log(txt3);
```
The output in the console would be:

```javascript
 ----------------whatever you shouldn't usually do.
    =-=-=-=/=-=-=-=/=-=-=-=/=-=-=-=/=-=-=-=/=-=-=-=/=-=-=-=/=-=-=-=/=-=-=-=/=-=-=-=
    whatever you shouldn't usually do.
```

* toggle()
This function provides a very simple way to display mutually exclusive strings depend‐ ing on a simple condition: whenever one value is passed as parameter, the other is returned:
```javascript
var txt = 'No';
txt = Ext.String.toggle(txt, 'Yes', 'No'); 
console.log(txt);
```
In the preceding code, the string Yes would be returned by the function.

* urlAppend()
This function takes care of serializing and appending encoded parameters to a URL:

```javascript
var sample = 'http://test.com/whatever'; 
var params = {
    key1: 'value1', 
    key2: true, 
    key3: 34536.65
};
var paramsTxt = Ext.Object.toQueryString(params); 
var txt = Ext.String.urlAppend(sample, paramsTxt); 
console.log(txt);
```
The preceding code would return the following: http://test.com/whatever? key1=value1&key2=true&key3=34536.65.

## Ext.mixin.Observable
The Ext.mixin.Observable mixin is the component that enables Sencha Touch objects to publish, fire, and listen to events; in the following example, the Animal class uses the mixin so that instances of it can listen to individual eating and beingEaten events, which are fired by the eat() method:

```javascript
// Defining the animal class
    Ext.define('Animal', {
        mixins: ['Ext.mixin.Observable'],
        config: {
            name: '(some animal name here)',
            kind: '(animal kind here)'
},
constructor: function(config) { this.initConfig(config);
},
eat: function (animal) {
// Here is where the magic happens this.fireEvent('eating', animal); animal.fireEvent('beingEaten', this);
} });
    // Creating two instances of the Animal class
var lion = Ext.create('Animal', { name: 'Simba',
        kind: 'Lion',
        listeners: {
                eating: function (prey) {
                var template = '{0} the {1} is eating {2} the {3}!';
                var name = this.getName();
                var kind = this.getKind();
                var preyName = prey.getName();
                var preyKind = prey.getKind();
                var txt = Ext.String.format(template, name, kind, preyName,
                preyKind);
                console.log(txt);
            }
} });
var zebra = Ext.create('Animal', { name: 'Pumbaa',
        kind: 'Warthog',
        listeners: {
            beingEaten: function (predator) {
            var template = '{0} the {1} is being eaten by {2} the {3}!'; 
            var name = this.getName();
            var kind = this.getKind();
            var predName = predator.getName();
            var predKind = predator.getKind();
            var txt = Ext.String.format(template, name, kind, predName, predKind);
            console.log(txt);
            } 
}
});
    // And now nature follows its path
lion.eat(zebra);
```

## Ext.util.DelayedTask
The Ext.util.DelayedTask provides a simple API around the standard setTime out() and clearTimeout() functions of the standard JavaScript library. DelayedTask instances can be delayed and canceled using a very simple API:

```javascript
var task = Ext.create('Ext.util.DelayedTask', function() { 
        console.log('This message took 1.5 seconds to appear');
});
task.delay(1500);
```
Predictably enough, the cancel() method can be called on tasks to prevent their execution.

`Tips` What is the difference with Ext.defer()?

    Ext.util.DelayedTask objects wrap a function to be called at some point in the future. These objects also expose the cancel() method, giving more control to the developer, as the task not only can be passed as parameter to other parts of the application, but it can also be canceled if needed. On the other hand, the Ext.Function.defer() provides a non-cancelable, one-shot execution of a function.

## Ext.util.HashMap
The Ext.util.HashMap is a container class that can be used in lieu of standard JavaScript literal dictionaries; it provides a very simple API and, most importantly, it exposes useful events that allow applications to be `notified` `when items are added or removed` from these containers.

* add(), clear(), remove(), removeByKey(), replace()
These methods are used to manipulate the items inside an Ext.util.HashMap instance. They all trigger the execution of the corresponding events:

* add
* clear
* remove 
* replace
The following code shows an example of use:

```javascript
var map = Ext.create('Ext.util.HashMap'); 
map.on('add', function (map, key, value, eOpts) {
        console.log('EVENT; item added; key: "' + key + '",
        value: "' + value + '"');
});
map.on('clear', function (map, eOpts) { 
    console.log('EVENT; map cleared');
});
map.on('remove', function (map, key, value, eOpts) {
        console.log('EVENT; item removed; key: "' + key + '",
        value: "' + value + '"');
});
map.on('replace', function (map, key, value, old, eOpts) { 
    console.log('EVENT; item replaced; key: "' + key + '", old value: "' + old +'", new value: "' + value + '"');
});
map.add('key1', 'value1'); 
map.add('key2', 1234); 
map.add('key3', true); 
map.clear();
map.add('key1', 'value1'); 
map.add('key2', 1234); 
map.add('key3', true); 
map.remove(true); 
map.removeByKey('key2'); 
map.replace('key2', 'new value');
```

* contains(), containsKey()
These two functions are used to test for the existence of values (or keys) in a particular instance.

```javascript
var map = Ext.create('Ext.util.HashMap'); 
map.add('key1', 'value1'); 
map.add('key2', 1234);
map.add('key3', true);
console.log('Map contains "value1" : ' + map.contains('value1'));
console.log('Map contains "key2" : ' + map.containsKey('key2'));
```

* each()
The each() method takes a function as parameter; this function is called for each key- value pair in the hash map following the iterator pattern.

```javascript
var map = Ext.create('Ext.util.HashMap'); 
map.add('key1', 'value1'); 
map.add('key2', 1234);
map.add('key3', true);
map.each(function (key, value) { 
    var txt = key + '> ' + value; 
    console.log(txt);
});
```

* get(), getCount(), getKeys(), getValues()
These functions are used to inspect the current state of the instance.

```javascript
var map = Ext.create('Ext.util.HashMap'); 
map.add('key1', 'value1'); 
map.add('key2', 1234);
map.add('key3', true);
console.log('Map has ' + map.getCount() + ' values'); 
var obj = map.get('key1');
console.log('object with key "key1" = "' + obj + '"'); 
var keys = map.getKeys();
console.dir(keys);
var values = map.getValues();
console.dir(values);
```

## Ext.util.Inflector
The Ext.util.Inflector class provides some utility functions that are used to manage the pluralization and singularization of words in English.

* isTransnumeral()
In English, a transnumeral word is a word that does not change when used in plural or singular forms.

```javascript
var words = ['fish', 'people', 'gents', 'women', 'happiness', 'sheep', 'code']; Ext.iterate(words, function (item) {
    var txt = [item, ' is transnumeral: ', 
    Ext.util.Inflector.isTransnumeral(item)].join(''); 
    console.log(txt);
}, this);
```
This yields the following console output:

```javascript
fish is transnumeral: true
    people is transnumeral: false
    gents is transnumeral: false
    women is transnumeral: false
    happiness is transnumeral: false
    sheep is transnumeral: true
    code is transnumeral: false
```

* ordinalize()
This function takes a number as a parameter and returns the proper ordinal string:
```javascript
var words = [1, 2, 3, 4, 5, 345, 456, 822, 21, 80]; 
Ext.iterate(words, function (item) {
    var txt = Ext.util.Inflector.ordinalize(item);
    console.log(txt); }, this);
```
The result is the following:
```javascript
    1st
    2nd
    3rd
    4th
    5th
    345th
    456th
    822nd
    21st
    80th
```

* pluralize(), singularize()
These functions take a word in either plural and singular form and return the opposite version.
```javascript
var singular = ['person', 'country', 'mouse', 'thing', 'boom']; 
Ext.iterate(singular, function (item) {
    var txt = ['One', item, 'many', 
    Ext.util.Inflector.pluralize(item)].join(' ');
    console.log(txt); }, this);
    var plural = ['people', 'countries', 'mice', 'things', 'booms']; 
    Ext.iterate(plural, function (item) {
        var txt = ['Many', item, 'one', Ext.util.Inflector.singularize(item)].join(' '); 
        console.log(txt);
    }, this);
```
This is the result:

```javascript
One person many people
One country many countries
One mouse many mice
One thing many things
One boom many booms
Many people one person
Many countries one country
Many mice one mouse
Many things one thing
Many booms one boom
```
