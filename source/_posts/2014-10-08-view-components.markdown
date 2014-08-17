---
layout: post
title: "View Components"
date: 2014-10-08 22:18:11 +0800
comments: true
categories: Sencha Touch
---
This chapter is all about view components. I will discuss how to implement messageboxes, lists, panels, toolbars, charts, and maps

In this chapter, you’ll learn how to implement:
* A messagebox Toolbars and title bars Buttons* Lists* A Google Map* Overlays Charts
#### Implementing a Messagebox
Unlike the standard JavaScript `alert` method, a messagebox in Sencha Touch is `asynchronous`. In standard JavaScript, the browser pauses execution until you press OK. In Sencha Touch, showing a messagebox will not cause the code to stop. For this reason, if you have code that should run only after some user feedback from the messagebox, you should use a callback function.
When you want to implement a messagebox, you will have to require Ext.MessageBox at the top of your view class. You don’t need to instantiate a messagebox (although you could); Ext.Msg is a global shared singleton instance of the Ext.MessageBox class that you can use in most cases:
The previous code describes the alert() method, which you can visually compare with the standard alert messagebox in JavaScript. See Figure 11-1.

{% img /images/2014/10/figure11-1.png%}

It displays a small window, with a title, a message, and an OK button. In addition to thealert, there are these types of messageboxes:
* prompt(), shown in Figure 11-2, which is a messagebox with a title, message, a textfield, and Cancel and OK buttons* confirm(), shown in Figure 11-3, which is a messagebox with a title, a message, and No and Yes buttons* show(), which provides even more options.
Here’s an example of the prompt() messagebox; the callback contains the button id and the entered value. The button id can be Yes, No, or Cancel (Cancel means close the messagebox without pressing the Yes and No buttons):
```
Ext.Msg.prompt('Welcome', 'Please enter your name', function(btn, val) { //callback
    console.log(btn, val);
});```
There is one more messagebox method that is worth mentioning: show().The show() method will give you many more options; you can pass in a config object. Here I set up
a customized prompt() messagebox. It has a width of 500 pixels, it accepts multiline input, it has an info icon, and I want to specify my own set of buttons. Also, I will define a callback function:```
var myPromptBox = Ext.Msg.show({
    title: 'Address',
    message: 'Please enter your address:',
    width: 500,
    buttons: Ext.MessageBox.YESNOCANCEL,
    iconCls: Ext.MessageBox.QUESTION,
    multiLine: true,
    prompt: {
        maxlength: 180,
        autocapitalize: true
    },
    fn: function(buttonId) {
        alert('You pressed the "' + buttonId + '" button.');
    }
});```
The iconCls can set an icon CSS class on the messagebox to showcase an icon. You can maintain this in your theme, but out of the box Sencha Touch ships with the following messagebox icons:
* Ext.MessageBox.ERROR (a round error sign) 
* Ext.MessageBox.INFO (a round info sign) 
* Ext.MessageBox.QUESTION (a question mark) 
* Ext.MessageBox.WARNING (a warning sign)

The following sets of buttons are available in Sencha Touch:

* Ext.MessageBox.OK (OK button with action UI skin)* Ext.MessageBox.YES (Yes button with action UI skin)* Ext.MessageBox.NO (No button)* Ext.MessageBox.CANCEL (Cancel button)* Ext.MessageBox.YESNOCANCEL (Cancel button, No button, and Yes button with action UI skin)* Ext.MessageBox.OKCANCEL (Cancel button, and OK button with action UI skin) * Ext.MessageBox.YESNO (No button, and Yes button with action UI skin)
If these button sets are in the wrong order, or you would like to specify your own button text and UI skins, you can pass in an array with Ext.Buttons into the buttons config, as you can see in Figure 11-4:
```
var pirateBox = Ext.Msg.show({
    title: 'Ahoy!',
    message: 'Stop pirate, would ye like t\' proceed?',
    iconCls: Ext.MessageBox.ERROR,
    width: 200,
    buttons: [{
        text: 'Aye',
        itemId: 'yes',
        ui: 'action'
    }, {
        text: 'Avast',
        itemId: 'no',
        ui: 'decline'
    }],
    fn: function(buttonId) {
        alert('You pressed the "' + buttonId + '" button.');
    }
});```
{% img /images/2014/10/figure11-4.png%}

To hide the previous messagebox, just run the hide() method on the pirateBox.

#### Implementing Toolbars and Title Bars
When you want to implement a toolbar (or title bar) view component (Figure 11-5), you will have to add the Ext.Toolbar (or Ext.TitleBar) to the requires at the top of your view class. After that, you can instantiate the component by using the Ext.create("Ext.Toolbar", {}) syntax, or by lazy instantiating (nesting the xtype called toolbar). Using the xtype is the best practice for coding real-world MVC applications.

{% img /images/2014/10/figure11-5.png%}

Implementing a title bar works exactly the same. The only difference is that the class from which you implement the instance is called Ext.TitleBar and the xtype is called titlebar. Although the title bar does not extend from Ext.Toolbar, they have a very similar appearance and setup.

The main difference between a Ext.TitleBar and a Ext.Toolbar is that the title config is always centered horizontally in a title bar, between any items aligned left or right. By default, any item in a title bar or toolbar is a button, so you do not need to explicitly set it. Items in a title bar, such as buttons, can be docked to the left or right side of the bar, via the align config.

#### Implementing Lists
A list is a data-aware component. You will need to hook up a data store to the list (with a fields model) to display any items. Following is an example of an Ext.List. First you must make sure you add the Ext.List to the requires array at the top of your view class so the Ext.Loader can load the framework class. Afterward you will use the object literal notation to create a Sencha Touch list:

```
{
    xtype: 'list',
    store: {
        fields: ['name'],
        data: [{
            name: 'Leonardo'
        }, {
            name: 'Donatello'
        }, {
            name: 'Michelangelo'
        }, {
            name: 'Raphael'
        }]
    },
    itemTpl: '{name}'
}
```
