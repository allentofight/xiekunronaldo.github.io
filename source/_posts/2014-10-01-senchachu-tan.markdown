---
layout: post
title: "Sencha初探"
date: 2014-10-01 22:12:09 +0800
comments: true
categories: Sencha Touch
---
[入门教程](http://vimeo.com/76568053)笔记
#### 创建app
cd到sencha touch的sdk(目前最新版本为touch-2.4.0)下执行以下命令

```
	sencha generate app MyApp /path/to/www/myapp
```
####Sencha+PhoneGap+Cordova

* PhoneGap和Cordova的安装

首选安装[NodeJS](http://nodejs.org/)安装以后执行以下命令

```
sudo npm install -g phonegap
sudo npm install -g cordova
```
即可安装[phonegap](http://phonegap.com/),[cordova](http://cordova.apache.org/)

####为Sencha App添加Cordova支持
进入Sencha app的根据目录,执行以下命令
```
	sencha cordova init [bundleID] [Native app name]
```
注意`Native app name`可以为空,这样编译到手机的名字即为sencha touch app的名字
执行完以上的命令后,`app.json`里会出现cordova的选项,如下图所示

```
"cordova" : {
                "config": {
                    // Uncomment the line below and add the platforms you wish to build for
                    //"platforms": "ios android",

                    "id": "com.doouya.growIndex",
                    "name": "NotesApp"
                }
            }
```
在`platforms`选项上添加你想支持的平台，这里我选了iOS,然后编译app

```
	sencha app build native
```
或者运行到真机上
```
	sencha app run native
```
或是在模拟器上运行

```
	sencha app emulate native
```
集成phonegap和Cordova后，我们可以看看集成是否成功,在app.js里的launch函数里写入以下代码
```
if (Ext.browser.is.PhoneGap){
	//TestCode
	alert(device.uuid);
}
```

注意这行`alert(device.uuid);`，现在phoneGap将native api剥离它的Core代码了,以plugin的形式管理,如上，如果你想用device，你需要将相应的plugin添加进来,以下为添加步骤:
* 进入Cordova的目录下
* 执行以下命令

```
	cordova plugin add org.apache.cordova.device
```
#### 项目开发中的问题
如果修改了app.css或新增css文件，重新编译一次以便生效

```
	sencha app build native
```
#### Workspace
类似xcode加的workspace,执行以下命令以产生一个workspace

```
	sencha generate workspace <../myworkspacefolder>
```
The generate workspace command will generate workspace metadata in the hidden .sencha folder, which contains the workspace classpath.It will copy the touch framework folder in the root of the workspace folder. The generate workspace command will also create two new (empty) folders: a packages folder, in case you want to download Sencha plug-ins, and a build folder, which will be the location where your build packages will be created and stored.Once you have a workspace, generating apps is the same as before:
```
cd <../myworkspacefolder>cd touchsencha generate app -name <namespace}> -path <../appfolder>```
`Tip`
If you want to upgrade to a newer version of Sencha Touch, you can download the latest version from the website and let Sencha Cmd update your folder structure. Here’s an example of how to upgrade the app (or workspace) from the command line:
```
	sencha app upgrade ../path-new-sdk-folder```When upgrading your framework, you don’t need to worry — it won’t touch your self-written classes. It basically just copies a newer version of the sdk in your application root or workspace, and it might change some metadata in the hidden .sencha folder. However, it’s also possible that it will need to update the app.js, app.json, or bootstrap.js files.
#### Generating a Model with Sencha Cmd
To create the model definition, you create a new JavaScript file and save it in the app/model directory. But a better choice is to have Sencha Cmd generate it for you.
Here’s the general syntax for implementing the model definition for a Sencha MVC application. On the command line, navigate to the app folder and run the following command:

```	sencha generate model <MyModelName> <fieldstring>
```
Make sure you already have an MVC application generated with Sencha Cmd. To generate our FindACab app model, navigate to your project folder and type the following:
```
	sencha generate model Cab id:auto,name:string,latitude:float,longitude:float, address1:string,phone:string,state_code:string,zip:string,city:string, userinput:string,country_code:string,avg_rating:float,distance:float```
Sencha Cmd will generate the code shown below

```
Ext.define('FindACab.model.Cab', { 
	extend: 'Ext.data.Model',	config: { 	fields: [		{ name: 'id', type: 'auto' },		{ name: 'name', type: 'string' },		{ name: 'latitude', type: 'float' },		{ name: 'longitude', type: 'float' },		{ name: 'address1', type: 'string' },		{ name: 'phone', type: 'string' },		{ name: 'state_code', type: 'string' },		{ name: 'zip', type: 'string' },		{ name: 'city', type: 'string' },		{ name: 'userinput', type: 'string'},		{ name: 'country_code', type: 'string' }, 		{ name: 'avg_rating', type: 'float' },		{ name: 'distance', type: 'float' }		] 	}});
```
The code in our newly generated JavaScript file defines the model Cab.js.
Every field name has a type; for example, the field name phone has a type string. The possible field types for a model are string, int, float, boolean, and auto. The type auto means there is no type conversion at all.
定义一个store

```
Ext.define('FindACab.store.Cabs', { 
	extend: 'Ext.data.Store', 
	config: {		model: 'FindACab.model.Cab',		autoLoad: true 	}});
```
`Tips`
Stores can be written without models. You can write the model in-line in the store by just defining a `fields` array

#### Generating a Controller with Sencha Cmd

```
	sencha generate controller <MyController>
```
#### Referencing a Component from a Controller
When you want to create a reference from the Sencha MVC controller to a component, you can either use refs or you can use the component query
Controller refs are available only in (of course) the controller

```
Example 6-3. Defining a Controller reference 
refs: {	myRef: 'somextype'}
```
The component query can be used anywhere in your code:

```Ext.ComponentQuery.query('somextype');```
Every ref that is created will automatically generate a getter. In Example 6-3, then, this.getMyRef() would find the component that has the xtype: "somextype".
#### Listening to Events from a Controller
When you want to control events in the Sencha MVC controller, add a control object to the controller’s config object. This control object contains a reference to a component (in this example, myref) and adds an event listener with some event that dispatches to myFunction:

```
control: { 
	'myref' : {	<event>: 'myFunction', }},
```
Once you have a reference to a component, you can bind an event to it. The trick is the control object, which listens to key/value pairs. For the key, you enter the reference name (the CSS-like selector, or it could also be the xtype); this key has another object as a value. This object accepts the name of the event as a key (for example, tap or initialize) and as a value, the string name of controller function that should be called after the event has happened.

```
Example 6-5. app/controller/CabController.js
Ext.define('FindACab.controller.CabController', { 
	extend: 'Ext.app.Controller',	config: {	models: ['Cab'],	stores: ['Cabs'],	refs: {		main: 'mainview'	}, 	control: {		'mainview': {			initialize: 'onInitMain',		}, 
		'button[action=press]': {			tap: 'onTapMain' }		} 		},	onInitMain: function() { 		console.log("Initialize mainview");	},	onTapMain: function() {		console.log("Tapped a button in mainview"); 	}});
```
The CSS-like selector button[action=press] invokes the controller method onTapMain(), which listens to all button xtypes that have the config action: press set.

#### Loading External Classes

In app.js, specify the paths to the classes in the Ext.Loader:

```
Ext.Loader.setPath({	'Ext': 'touch/src',	'FindACab': 'app'	'<new namespace>': '<folder-to-point-to>'});
```
This doesn’t load anything in memory yet; it just tells the Sencha Touch loader where to find files that use the Utils namespace. You will use the Utils.Commons class in your app so the Sencha Touch loader needs to load the class in its memory. Therefore, you add Utils.Commons to the requires array in the app.js.

The Utils.Commons class is just a class with static members. I can access the defined properties from anywhere in my code. For example, in the FindACab app, I will need to have global access to the YELP_API URL. If I add it to the Utils.Commons class, I can access it from anywhere in my code by calling Utils.Common.YELP_API. The code for Utils.Commons will look like Example 6-7.

```
Ext.define('Utils.Commons', { 
	statics: {		YELP_API: 'http://api.yelp.com/business_review_search?', YELP_KEY: 'ftPpQUCgfSA3yV98-uJn9g',		YELP_TERM: 'Taxi',		LOCATION: 'Amsterdam NL',
		getUrl: function() {			return this.YELP_API + "term=" + this.YELP_TERM +
					"&ywsid=" + this.YELP_KEY + 
					"&location=" + this.LOCATION;
		}
	}
});		
```
Using the Ext.Loader is really powerful. You no longer have to worry about loading scripts in the correct order.
It’s a really good thing that the Sencha Touch loader takes care of this. However, you do need to tell the Ext.Loader, “Hey loader, I require this class, so don’t forget to load it.” You can give this instruction by setting the requires array in a class 

`Note`
When you’re adding Sencha classes to your project and they are not saved within the app folder, you will also need to specify the paths to the classpath used for the build process; otherwise, your builds will fail.
There is an `app` and `workspace classpath`. The workspace classpath matters when you have multiple applications and you want to share code; in all other situations, the app classpath should be good enough.

In Chapter 14, you will add the utils folder to the app classpath in the hidden .sencha/sencha.cfg file so the FindACab app can be built:

```
	app.classpath=${app.dir}/app.js,${app.dir}/app,${app.dir}/utils
```
You can forget to inform the `Ext.Loader` about your dependencies, or just ignore it. When you run your application in debug mode (you can enable it in the app.json file by loading the sencha-touch-debug.js framework with debug messages instead of sencha- touch.js), the console will throw some warnings and indicate which classes you need to require

#### Implementing a Form

```
	sencha generate form -name MyForm -fields field,field2:textfield
```