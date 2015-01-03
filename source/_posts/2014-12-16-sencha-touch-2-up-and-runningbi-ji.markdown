---
layout: post
title: "Sencha Touch 2 Up and Running笔记"
date: 2014-12-16 20:14:23 +0800
comments: true
categories: Sencha Touch
---
# Views


## Events

One of the most useful things about components is that they can trigger events. Events in Sencha Touch view components can be added in two separate locations:

* On the view definition itself, using the listeners configuration entry
* On a separate controller class
You can mix and match both approaches; this means that when an event is fired, all the functions configured as event handlers for that particular event will fire
The following code shows a very simple event handler for a button:
```javascript
{
        xtype: 'button',
        text: 'Action button',
        listeners: {
            tap: function (button, e, eOpts) { 
                console.log('Button was tapped');
            } 
        }
}
```
Typical events in Sencha Touch include the following:

* The `initialize`, `hide`, and `show` events in the Ext.Component class
* The add and remove events in the Ext.Container class, which are triggered every time subviews are added or removed from a parent component
* The `scrollstart`, `scroll`, and `scrollend` events in the Ext.scroll.Scroller class, which is used internally by components that are scrollable
* The `tap` event in the Ext.Button class (as shown in the previous example)
* The `itemtap`,`itemsingletap`,`itemdoubletap`,and `disclose` events intheExt.data.List class
* The `maprender` event in the Ext.Map
* The `toggle` event in the Ext.SegmentedButton class
* The `play`, `pause`, `stop`, and `ended` events in the `Ext.Video` and `Ext.Audio` classes
* The `back`, `pop`, and `push` events in the `Ext.navigation.View` class
* The `focus` and `blur` events in the Ext.field.Text class (to be studied in detail in Chapter 5

## Layouts

There are several layouts available:
* fit, which is the simplest of all the available layouts, and which makes the child component completely fit the available space of its parent
* hbox, which stacks child views horizontally
* vbox, used to stack child components vertically
* card,used to stack several components on the same structure but display only one at a time (particularly useful in smartphone screens)
Additionally, every layout is able to dock components, which is used to force some components to be placed at the edges (top, right, bottom, or left), resizing the other components if required.

`fit` layout demo:

```javascript
Ext.define('Chapter3Layouts.view.FitDemo', {
        extend: 'Ext.Container',
        xtype: 'fitdemo',
        config: {
            layout: {
                type: 'fit'
            },
        items: [{
                xtype: 'component', margin: 10,
                html: 'This component is set to fit',
                style: 'background-color: lightgray'
        }] }
});
```

{% img /images/2014/12/Figure3-1.png%}
The second layout is hbox, which is very similar to vbox, with only a major difference: the orientation of the resulting layout. In both cases, though, the child views `must` have a `flex` configuration parameter

```javascript
 Ext.define('Chapter3Layouts.view.HBoxDemo', {
        extend: 'Ext.Container',
        xtype: 'hboxdemo',
        config: {
            defaults: {
                margin: 10
}, layout: {
                type: 'hbox'
            },
            items: [{
                xtype: 'component', flex: 1,
                html: 'Flex: 1', style: 'background-color: pink'
            }, {
                xtype: 'component', flex: 3,
                html: 'Flex: 3', style: 'background-color: lightgray'
}] }
});
```

{% img /images/2014/12/Figure3-2.png%}

## Templates
The Sencha Touch template system is based in the `Ext.XTemplate` class and is also baked in the `Ext.Component` class through the `tpl` configuration parameter.
```javascript
Ext.define('Chapter3Layouts.view.TemplatesDemo', {
        extend: 'Ext.Container',
        xtype: 'templatesdemo',
        config: {
            scrollable: true, 
            data: {
                countries: [{
                    country: 'Argentina', flagColors: ['light blue', 'white']
                }, {
                    country: 'Switzerland', flagColors: ['red', 'white']
                }, {
                    country: 'France', flagColors: ['blue', 'white', 'red']
                }] 
            },
            tpl: Ext.create('Ext.XTemplate', '<tpl for="countries">',
                '<div>{#}. {country}: {flagColors:this.join}</div>',
            '</tpl>', {
                join: function (value) { 
                    return value.join(', ');
                } })
} });
```
{% img /images/2014/12/Figure3-3.png%}

## Component Library Catalog
Sencha Touch, like many other large, “enterprisey” programming frameworks, offers an incredible library of `components` ready to be used in applications

The diagram in Figure 3-7 shows a simplified overview of the hierarchy of view classes available in Sencha Touch 2.1 (for the sake of readability, not all view classes are represented in the diagram).This chapter considers three major families of components:

* Basic components, inheriting from Ext.Component
* Containers, inheriting from Ext.Container
* Panels, inheriting from Ext.Panel
This chapter also adds yet another group to these families: the “Multimedia” compo‐ nents, allowing developers to include complex visualizations such as audio, video, or graphs. These components are usually just subclasses of Ext.Component, but they de‐ serve a place of their own, as they encapsulate exceptionally useful functionality behind surprisingly simple interfaces.

{% img /images/2014/12/Figure3-7.png%}

* Components
All components in Sencha Touch (which basically means anything you can see on the screen of your Sencha Touch application) can be configured using these properties:
* The `centered` property is a Boolean value that specifies whether the current instance must be centered inside its parent container.
* The `contentEl` propertyallowsdeveloperstopopulateaSenchaTouchcomponent with any HTML element available in the page; the property can hold an Ext.Ele ment object, a DOM reference, or a string with an element ID.
* The `disabled` property is a Boolean that specifies whether the current component is active or not, to receive user input and to react to events. Please pay attention to the fact that there is `no enabled property`!
* The `docked` property specifies the location where to anchor a particular component on a complex layout; the valid values for this parameter are top, bottom, left, and right.
* The `flex` property is used by the hbox and vbox layouts to specify the proportion of screen real estate to be reserved to a particular component. Please refer to the “Layouts” section later in this chapter for more information about them.
* The `fullscreen` property is used when a component is directly added to the Ext.Viewport singleton instance of the application; this Boolean specifies whether the component in question has to stretch itself to occupy the whole space available.
* The `hidden` property specifies whether the current component is visible or not. Please pay attention to the fact that there is no visible property available!
* The `hideAnimation` and `showAnimation` properties provide very simple ways to apply animations to your user interfaces. You can specify either a string with the name of an animation (such as fadeIn or pop) or a `complex` object representing an `instance of the Ext.Anim` class.
* The `itemId` property should always be preferred instead of the more common id property; this is because the itemId property requires `uniqueness` of value `only among child components of a container`, while the `id` property requires `all values` to be different throughout the whole application, which can be difficult to use. The values of the `itemId` property are usually used with the `Ext.Container.getComponent()` instance function, which is used to retrieve pointers of child components inside a container.
* `maxHeight`, `maxWidth`, `minHeight`, and `minWidth` are used to constrain the size of the current instance, making sure that the automatic layout process of the application respects some basic boundaries.
* The `style` property allows developers to inject any kind of CSS string that they wish directly into the application. Although this is convenient, it would be wiser to use the `cls` property instead, which specifies additional CSS classes, defined in an ex‐ ternal CSS file.
* Finally, the `styleHtmlContent` Boolean property (whose value is false by default) specifies whether the value of the html property can be styled or not (for example, when using HTML text that contains <strong>, <em>, or <a> tags, which carry their own styling semantics).

## Button
using the Ext.But ton class (or the button xtype):
```javascript
{
    xtype: 'button', 
    text: 'Reply', 
    badgeText: '42', 
    ui: 'normal', 
    iconCls: 'reply', 
    iconMask: true, 
    iconAlign: 'top', 
    margin: '5 5 5 30', 
    listeners: {
        tap: function (button, e, eOpts) { 
            console.log('button tap');
        } 
    }
}
```
The result of the preceding code can be seen in Figure 3-8.

{% img /images/2014/12/Figure3-8.png%}
* The ui property can take several values: `action`, `back`, `confirm`, `decline`, `forward`, `normal`, `plain`, and `round`. Applying any of these values will change the shape and color of the button, providing context and hints about the functionality of the but‐ ton to the user. 
* Buttons can have icons applied to them.Using the `iconCls` property together with the iconMask: true property, you can use a large array of default icons provided by Sencha Touch: action, add, arrow_down, arrow_left, arrow_right, arrow_up, bookmarks, compose, delete, download, favorites, home, info, locate, maps, more, organize, refresh, and reply. See Figure 3-10 for some examples.

{% img /images/2014/12/Figure3-8.png%}

`warning`
Remember to set `iconMask:` true whenever using the default icons provided by Sencha Touch; otherwise these will not be visible in the button.

## Containers

* Carousel
* Navigation view
* Segmented button
* Tab panel
* Title bar
* Toolbar
In addition to the configuration options provided by the Ext.Component class, con‐ tainers can be customized using several more properties:
* The `defaultType` property specifies the `xtype` of the child elements of a particular container; for example, toolbars expect their children to be buttons, while tab panels expect them to be just components, and so on. You can define your own expected value for this property, and automatically all components defined inside the items property will be instances of that type (unless you specify a custom xtype, of course).
* The `defaults` property specifies common properties for all the child components defined inside the current container.
*  Finally, the `scrollable` property specifies whether the component can scroll to reveal child components not visible otherwise.

### Navigation view
The navigation view itself is a container that does not show lots of UI chrome to the users besides the `navigation bar` and the `Back button` itself. However, it offers a very interesting interface to application developers, who can programmatically use the `push()` and `pop()` functions to add and remove views, triggering an animation and the display of the next screen in the sequence.

### Segmented button
Creating a segmented button, as usual, is a straightforward task:

```javascript
{
        xtype: 'segmentedbutton',
        items: [{
            text: 'Hue'
        }, {
            text: 'Saturation'
        }, {
            text: 'Brightness'
        }]
}
```

### Tab panel
Thanks to the `activeitemchange` event (defined in the Ext.Container class) developers can be notified of the fact that the user has selected a different screen using the tab bar.
### Title bars and toolbars
Title bars (represented by the Ext.TitleBar class or the titlebar xtype) and toolbars (Ext.Toolbar, or simply toolbar) are very similar components; the `main difference` between them is that the `title` configuration in the title bar is always `centered`, while the toolbar does not make this assumption.

The following source code shows how to create a very simple toolbar with some buttons in it:
```javascript
 {
        xtype: 'toolbar', docked: 'bottom',
        items: [{
            iconCls: 'favorites', iconMask: true
        }, {
            xtype: 'spacer'
        }, {
            iconCls: 'search', 
            iconMask: true 
        }]
}
```
To create a title bar, it is as simple as usual; in this case, the title will be automatically centered.
```javascript
{
        xtype: 'titlebar', docked: 'top', title: 'Top',
        items: [{
            iconCls: 'action', iconMask: true, align: 'left', text: 'Action' 
        }, {
            iconCls: 'user', iconMask: true, align: 'right', text: 'Profile' 
        }]
}
```
The result of the preceding code snippets can be seen in Figure 3-15.
{% img /images/2014/12/Figure3-15.png%}

### Panels
Panels (instances of the Ext.Panel class, with the xtype panel) are a special kind of container that can be used as overlay views, floating on top of other components. The most important API they offer developers is the `showBy()` function, which takes any other component as a parameter and makes the panel appear “pointing” to it
```javascript
 {
        xtype: 'button',
        text: 'Touch to open panel',
        itemId: 'openPanelButton',
        listeners: {
            tap: function (button, e, eOpts) { 
                if (!button.panel) {
                    button.panel = Ext.widget('panel', {
                        html: 'This is a panel',
                        padding: 20,
                        width: 280,
                        modal: true, 
                        hideOnMaskTap: true, // 
                        hidden: true, 
                        hideAnimation: 'fadeOut', 
                        showAnimation: 'fadeIn'
                }); 
            }
                button.panel.showBy(button);
            }
        } 
}

```
{% img /images/2014/12/Figure3-16.png%}
Many components in Sencha Touch extend panels to display fancy overlays in applications:
* Action sheets
* Message boxes 
* Pickers
* Sheets

#### Action sheet
```javascript
{
    xtype: 'button',
    text: 'Touch to open action sheet',
    itemId: 'openButton',
    listeners: {
        tap: function(button, e, eOpts) {
            if (!button.sheet) {
                button.sheet = Ext.widget('actionsheet', {
                    items: [{
                        text: 'Delete',
                        listeners: {
                            tap: function(btn, e, eOpts) {
                                button.sheet.hide();
                            }
                        }
                    }, {
                        text: 'Approve',
                        ui: 'confirm',
                        listeners: {
                            tap: function(btn, e, eOpts) {
                                button.sheet.hide();
                            }
                        }
                    }, {
                        text: 'Cancel',
                        ui: 'decline',
                        listeners: {
                            tap: function(btn, e, eOpts) {
                                button.sheet.hide();
                            }
                        }
                    }]
                });
                Ext.Viewport.add(button.sheet); // }
                button.sheet.show(); // }
            }
        }
    }
}
```
#### Message box

1. alert(), which displays a title, a text and an OK button to dismiss the dialog
2. confirm(), which prompts the user to answer a single question by “Yes” or “No” 
3.prompt(), which can be used to ask the user for some input

`warning`
    These dialog boxes are asynchronous
In spite of sharing the same name as their “native” JavaScript counter‐ parts, the dialog boxes exposed by the Ext.Msg singleton object are asynchronous; this has several consequences:

1. The execution of the calling JavaScript code is not blocked until the user dismisses the dialog boxes, which in turn means that any network callbacks or other events are still being processed and ex‐ ecuted.
2. To retrieve the input from the user, whenever any of these dialog boxes are dismissed, you have to provide a callback function that will be executed as soon as the user taps any of the buttons provided.
#### Picker

```javascript
 {
     xtype: 'button',
     text: 'Touch to show picker',
     listeners: {
         tap: function(button, e, eOpts) {
             if (!button.picker) {
                 button.picker = Ext.widget('picker', {
                     slots: [{ //
                         name: 'first_name',
                         title: 'First name',
                         data: [{
                             text: 'John',
                             value: 'john'
                         }, {
                             text: 'James',
                             value: 'james'
                         }, {
                             text: 'Paul',
                             value: 'paul'
                         }, {
                             text: 'Michael',
                             value: 'michael'
                         }]
                     }, {
                         name: 'last_name',
                         title: 'Last name',
                         data: [{
                             text: 'Smith',
                             value: 'smith'
                         }, {
                             text: 'Raymond',
                             value: 'raymond'
                         }, {
                             text: 'Graham',
                             value: 'graham'
                         }, {
                             text: 'Fowler',
                             value: 'fowler'
                         }]
                     }],
                     listeners: { //
                         cancel: function(picker, opts) {
                             Ext.Msg.alert('cancel', 'Picker has been canceled');
                         },
                         change: function(picker, value, opts) {￼￼
                             Ext.Msg.alert('chance', 'Picker has value: ' + Ext.JSON.encode(value));
                         }
                     }
                 });

                 Ext.Viewport.add(button.picker); // 
             }
             button.picker.show(); // 
         }
     }
 }]
```

{% img /images/2014/12/Figure3-19.png%}

#### Sheet
The Ext.Sheet class (xtype sheet) provides a generic panel that appears animated on the screen and can display any kind of content. It is the base class of the Ext.Action Sheet class previously described.
```javascript
{
    xtype: 'button',
    text: 'Touch to show sheet',
    itemId: 'openButton',
    listeners: {
        tap: function(button, e, eOpts) {
            if (!button.sheet) {
                button.sheet = Ext.widget('sheet', {
                    html: 'Some text inside of the sheet',
                    style: 'color: white; font-weight: bold',
                    stretchX: true, //flag
                    hideOnMaskTap: true
                });
                Ext.Viewport.add(button.sheet);
            }
            button.sheet.show();
        }
    }
}
```
`warning`
    `flag`:This parameter makes the sheet appear at the top of the application, next to the status bar. Otherwise, the sheet appears by default from the bottom, animating itself until it reaches the center of the screen.

## Multimedia Views

* Images 
* Audio 
* Video 
* Charts 
* Maps

### Image
The Ext.Image class (xtype image) allows the placement in applications of individual images, just like the <img> tag allows it in standard HTML.
```javascript
{
    xtype: 'image',
    src: '/img/icons/Icon@2x.png',
    height: 114,
    width: 114
}
```

### Audio
The Ext.Audio class (xtype audio) wraps the HTML5 <audio> tag, supported by most major mobile web browsers these days. The configuration and the creation of a new instance of this class are straightforward, and the result is shown in Figure 3-21:

{% img /images/2014/12/Figure3-21.png%}
The supported formats for audio are the following:
* Uncompressed WAV and AIF audio
* MP3 audio 
* AAC-LC
* HE-AAC audio

`warning`

When used in Android devices, the audio tag does not provide native playback controls like in iOS; developers should then provide their own controls, and use the play(), pause(), stop(), and toggle() methods provided by the Ext.Media class instead.

### Video
Similarly to the audio element, the Ext.Video class (xtype video) can be used to wrap the native <video> tag provided by HTML5 in most major mobile web browsers.

```javascript
 {
        xtype: 'video',
        itemId: 'videoComponent',
        url  : 'res/video/iphone.m4v',
        posterUrl: 'res/video/jerome.png'
}
```

`warning`
Just like with the Ext.Audio class, you must pay attention to the video formats supported in each platform; unfortunately, iOS and Android have different requirements, so it is very important to read the docu‐ mentation in each case and to provide the formats that are supported in each case.
Moreover, many older Android devices have known issues with video playback, which means that testing is required to make sure that the application behaves correctly in the supported devices for your application.

### Charts
Sencha Touch 2 includes powerful 2D charting capabilities. As you have guessed, charts are represented by instances of the Ext.chart.AbstractChart class, of which three subclasses provide the most useful functionality:
* `Ext.chart.CartesianChart` (xtype chart) represents simple charts using the “x” and “y” Cartesian axes.
* `Ext.chart.PolarChart` (xtype polar) is used to display charts based on polar or radial coordinates.
* `Ext.chart.SpaceFillingChart` (xtype spacefilling) useful to display charts (without axes) that take all the available space in the component.

`warning`
    Ext.draw.Component
    itself, the `Ext.chart.AbstractChart` class inherits from `Ext.draw.Com ponent`, itself a subclass of Ext.Container. The Ext.draw.Component, as the name implies, allows developers to draw any kind of object on the UI of their applications. The Ext.draw.Component class is beyond the scope of this book, but suffice it to say that it wraps the Scalable Vector Graphics (`SVG`) and `<canvas>`` functionality of the underlying browser, allowing an application to render any kind of visual content on the screen.

### Maps
Sencha Touch wraps the Google Maps API, allowing developers to include any kind of maps in their applications.

```html
<script type="text/javascript" src="http://maps.google.com/maps/api/js?sensor=true"></script>
```
Once the standard Google Maps API JavaScript file is included, adding a map view to a Sencha Touch application is a very simple task:
```javascript
Ext.define('Chapter3Views.view.MapDemo', {
    extend: 'Ext.Panel',
    xtype: 'mapdemo',
    config: {
        layout: {
            type: 'card'
        },
        items: [{
            xtype: 'map',
            useCurrentLocation: true
        }]
    }
});
```
If the application requires markers or other type of embellishment on the map, then developers must use the `standard markers API` provided by Google. Sencha Touch does not provide any wrapper functionality to simplify this task
```javascript
{
    xtype: 'map',
    useCurrentLocation: false,
    mapOptions: {
        zoom: (Ext.os.is.Phone) ? 6 : 8, //
        center: new google.maps.LatLng(46.801111, 8.226667) // },
        listeners: {
            maprender: function(component, googleMap, eOpts) {
                var store = Ext.getStore('citiesStore');
                var markers = [];
                store.each(function(city, index, length) { //
                        var position = new google.maps.LatLng(city.get("latitude"), city.get("longitude"));
                        var marker = new google.maps.Marker({
                            position: position,
                            city: city
                        });
                        marker.setClickable(true);
                        markers.push(marker);
                        google.maps.event.addListener(marker, 'mouseup', function() {
                                var city = this.city;
                                var content = '<span style="font-weight: bold">' + city.get('name') + '</span>';
                                if (!component.infoWindow) {
                                    component.infoWindow = new google.maps.InfoWindow(); // }
                                    component.infoWindow.setContent(content);
                                    component.infoWindow.open(googleMap, this);
                                });
                        });
                    var mcOptions = {
                        gridSize: 50,
                        maxZoom: 16
                    };
                    var markerCluster = new MarkerClusterer(googleMap, markers,
                        mcOptions); // 
                }
            }
        }
```
{% img /images/2014/12/Figure3-24.png%}

### Creating Views
Choosing a Base Class
UI components in Sencha Touch can usually be grouped in the fol‐ lowing families:
* Components 
* Containers
* Panels
When faced with the creation of a new visual element, developers should ask themselves some very simple questions to find the proper base class to extend:
1. Should the new widget appear floating or on top of other components? If so, it should extend Ext.Panel. If not, continue to question 2.
2. Shouldthenewwidgetcontainothercomponents?Ifso,thenitshouldbeasubclass of Ext.Container.
3. If the answer to both the previous questions is “no,” then your new component should just extend Ext.Component.

In the case of the AkoLib.view.SplitView class, the answers for the questions are the following:
1. No 
2. Yes

##### Creating the Class Skeleton

```javascript
Ext.define('AkoLib.view.SplitView', {
    extend: 'Ext.Container',
    xtype: 'akosplitview',
    config: {},
    initialize: function() {
        this.callParent(arguments);
    }
});
```

warning `The initialize function`
 the first thing to do every time you override a function pro‐ vided by Sencha Touch is to call `this.callParent(arguments)`, so that the behavior provided by the base class is executed before your own. Doing so ensures that every instance behaves in a `predictable` way.

 ##### Adding Configuration Options
 We are going to add some configuration options to our component, all of which are completely customizable by developers using it:
```javascript
 Ext.define('AkoLib.view.SplitView', {
     extend: 'Ext.Container',
     xtype: 'akosplitview',
     config: {
         // Custom properties
         masterView: null,
         detailView: null,
         screenTitle: 'Sample Split View',
         menuButtonTitle: 'Menu',
         detailToolbarButtons: null,
         collapsesMasterView: null,
         showToggleButton: false,
         collapsed: false,
         // Common properties for components
         itemId: 'splitView',
         layout: 'hbox',
         items: []
     },
     initialize: function() {
         this.callParent(arguments);
     }
 });
```

`About setting the itemId`
As shown in the example, we are not specifying the `id` property, but rather the `itemId` property. The distinction between those two is important, as Sencha Touch requires the id property to any two objects in an application to be strictly different. Using the `itemId` property in your own components ensures that Sencha Touch is able to generate `different ID` values for `different instances` of the same class.

#### Providing Initialization
Whenever a new instance of this component is created, we want to set some event handlers `that cannot be set using standard configuration options`; we are going to use the initialize() function for that:
```javascript
initialize: function() {
    this.callParent(arguments);
    var o = Ext.Viewport.getOrientation();
    this.handleOrientationChange({
        orientation: o
    });
    Ext.Viewport.onBefore('orientationchange', 'handleOnBeforeOrientationChange', this, {
        buffer: 50
    });
    Ext.Viewport.on('orientationchange', 'handleOrientationChange', this, {
        buffer: 50
    });
    // ...
    var showMenuButton = this.getShowMenuButton();
    showMenuButton.setText(this.getMenuButtonTitle());
    showMenuButton.addListener('tap', function(button, e, eOpts) {
        this.getOverlayView().showBy(button, 'tl-bc');
    }, this);
    // ...
}
```
# Data

the definition of a Person class:
```javascript
 Ext.define('Chapter4Data.model.Person', {
     extend: 'Ext.data.Model',
     config: {
         fields: [{
             name: 'entryId',
             type: 'int'
         }, {
             name: 'firstName',
             type: 'string'
         }, {
             name: 'lastName',
             type: 'string'
         }, {
             //
             // ... snip ...
             //
         }, {
             name: 'enabled',
             type: 'boolean',
             defaultValue: true
         }, {
             name: 'createdOn',
             type: 'date'
         }],
         idProperty: 'entryId',
         belongsTo: 'Company',
         hasMany: {
             model: 'Orders',
             name: 'orders'
         },
         validations: [{
             type: 'presence',
             field: 'firstName'
         }, {
             type: 'presence',
             field: 'lastName'
         }, {
             type: 'length',
             field: 'description',
             min: 15
         }, {
             type: 'inclusion',
             field: 'role',
             list: ['User', 'Admin']
         }]
     }
 });
```
As previously shown, a model class definition consists of two major configuration options:

1. The list of the data fields that each instance of this class must store;they are represented as literal objects with name and type entries.
2. An `idPropertyentry`,indicating the field that is used to uniquely identify each entry in a store. This field is extremely important, since as we are going to see in a minute, Sencha Touch stores use this information pervasively.

You can also specify other parameters together with the name and the type of a field; for example, developers can add information about default values using the defaultVal ue property; other useful properties are `allowNull`, `sortDir` (which can take the values `ASC` or `DESC`), or `dateFormat` (for date fields).

## Model Field Types
Sencha Touch models can have fields with the following types:

* int
* string 
* float
* boolean 
* date

## Associations
This is the part of the book where Ruby on Rails developers will feel at home. Sencha Touch allows us to define relationships among model classes, using two very common configuration properties: `belongsTo` and `hasMany`, which are self-explanatory.
```javascript
var person = Ext.create('Chapter4Data.model.Person') 
person.set('firstName', 'Aaron');
person.set('lastName', 'Schwartz');
var company = person.getCompany(function(company) {
    console.log('Here is the company information: ' + company.get('name'));
});
person.orders().add({
    item: 234,
    value: 699.00,
    note: 'To be delivered next month'
});
person.orders().sync();
```
The preceding script shows that the `belongsTo` relationship `automatically` generates a `getCompany()` function, which works asynchronously

## Validations
models can carry their own validation logic. Using the validations key in the configuration of a model, we can ensure that new instances are valid or not at any given time, providing a unified, simple API for developers.

There are many kinds of possible validations, all defined as functions in the Ext.da ta.Validations singleton object:

* email, used to check the proper formatting of email addresses
* inclusionandexclusion,whichensurethatthevalueprovidedisincludedornot
included in a predefined list
* format, making sure that a particular string conforms to some regular expression
* length, which as the name implies verifies the length of a particular string field 
* presence, used to check the existence of a particular value in a model instance
During runtime, applications can use the validate() method on model instances to trigger all the required validations and to return an object containing all the information about any errors found during the procedure:
```javascript
var person = Ext.create('Chapter4Data.model.Person') person.set('firstName', 'Aaron');
person.set('lastName', 'Schwartz');
// Perform validation
var validationResults = person.validate();
console.log('Is valid? ' + validationResults.isValid());
console.log('All problems: ' + validationResults.items);
console.log('Specific problem: ' + validationResults.getByField('email'));
```

## Stores and Proxies

 * Stores can be thought of as managed sets of model instances;for example,youcan have a store containing all the instances of the Person class in your application. There are two basic types of stores: `linear` and `hierarchical`.

 * Proxies,on the other hand,encapsulate the logic required to connect to a localor remote data source, such as the browser’s localStorage object, or a REST web service elsewhere on the network.

 There are two kinds of proxies available in Sencha Touch: local and remote. We are going to learn more about these two groups in the following sections.

### Local Proxies
 Local proxies are used by stores to keep sets of model instances in the local browser. There are three kinds of local proxies:

* Memory
* LocalStorage
* SessionStorage

#### Memory
The memory proxy, represented by the Ext.data.proxy.Memory class, is used only as a simple in-memory storage option that is not persisted across page refreshes.
```javascript
 Ext.define('Chapter4Data.store.MemoryPeopleStore', {
     extend: 'Ext.data.Store',
     xtype: 'peoplestore',
     config: {
         model: 'Chapter4Data.model.Person',
         storeId: 'peopleStore',
         autoLoad: true,
         sorters: [{
             property: 'firstName',
             direction: 'ASC'
         }],
         proxy: {
             type: 'memory',
             reader: {
                 type: 'json',
                 root: 'people'
             }
         }
     }
 });
```
You can use the Memory proxy to keep small amounts of in-memory temporary data. However, the `SessionStorage` proxy (described later in this section) is a better choice for larger quantities of temporary data.

#### LocalStorage

```javascript
Ext.define('Chapter4LocalStorage.store.PeopleStore', {
    extend: 'Ext.data.Store',
    xtype: 'peoplestore',
    config: {
        model: 'Chapter4LocalStorage.model.Person',
        storeId: 'peopleStore',
        autoLoad: true,
        autoSync: true,
        sorters: [{
            property: 'firstName',
            direction: 'ASC'
        }],
        proxy: {
            type: 'localstorage',
            id: 'peopleproxy'
        }
    }
});
```
