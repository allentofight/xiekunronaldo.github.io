---
layout: post
title: "Offline Storage (Client Proxies)"
date: 2014-10-07 22:09:31 +0800
comments: true
categories: Sencha Touch
---

HTML5 offers a powerful way to achieve this with Local Storage, Session Storage, Web SQL, and Application Cache.

* HTML5 Local Storage

	HTML5 Local Storage saves its data with no expiration date. You use it to save key/value pairs. The Sencha framework uses it internally to save persisted JavaScript (Sencha) classes. When you open a Sencha Touch app while you are offline, the Sencha Touch app itself can run because all the assets are stored offline in the Local Storage. The FindACab app makes use of Local Storage by saving the user settings offline.

* HTML5 Session Storage

	This method saves its data in a session. The session will be over after the browser is closed. This is the main difference from Local Storage, which won’t lose its data at all. Usually, the HTML5 Session Storage is used for online shopping baskets. It stores all the products in the session. After the products are purchased, the session can be cleared.

* Web SQL Databases

	This method, formerly known as “WebDB,” provides a thin wrapper around a SQL database. In 2007, Google launched Google Gears, an open source cross-browser plug-in that included an embedded database based on SQLite. This early prototype later influenced the creation of the HTML5 Web SQL Database specification.

* Application Cache

	This can make your application available offline, and can cache files and assets into the browser. You will need to `set up an AppCache manifest file`.

`CAUTION`

There is a SQL client proxy available in Sencha Touch 2.3. However, unfortunately, on November 18, 2010, the W3C announced that Web SQL database is a deprecated specification. This means that web developers should no longer use the technology, as the spec will receive no further updates, and browser vendors aren’t encouraged to support this technology. You can still use Web SQL databases in Google Chrome and in Safari browsers, but at your own risk. That said, it works very well with Sencha Touch, and there is no officially supported alternative.You might want to check out IndexDB instead. See Grgur Grisogono’s [Sencha IndexDB Proxy](https://github.com/grgur/Ext.data.proxy.IndexedDB).
The Sencha Touch client proxy saves and loads the data locally on its device or in itsmemory. Sencha Touch’s client proxy has four important subclasses:
* Ext.data.proxy.Memory 
* Ext.data.proxy.LocalStorage 
* Ext.data.proxy.SessionStorage 
* Ext.data.proxy.Sql
The `MemoryProxy` uses a local variable for data storage and retrieval, but the contents are lost on every page refresh. For that reason, I will not discuss it in this chapter.
This is not the case with the `LocalStorage` proxy, the `SessionStorage` proxy, and the `SQL` proxy. When the browser provides support for these, it will save the model data as a key/value data store into the browser, so it will remain after the browser is closed and reopened. If the HTML5 Storage API is not supported by the browser, the constructor will throw an error.

This is not the case with the LocalStorage proxy, the SessionStorage proxy, and the SQL proxy. When the browser provides support for these, it will save the model data as a key/value data store into the browser, so it will remain after the browser is closed and reopened. If the HTML5 Storage API is not supported by the browser, the constructor will throw an error.
Because the data is saved as key/value pairs, where the key needs to be unique, you cannot save complex objects like JSON — unless you convert these JSON objects to strings with Ext.JSON.encode(). LocalStorageProxy automatically serializes and deserializes data when saving and retrieving it.
Here is an example showing how to implement a client proxy to your model or store class:
```
proxy: {	type: "<client-proxy>"}```
The type can be set to `memory` (the default), `localstorage`, `sessionstorage`, or `sql`.
In this chapter, you’ll learn:
* How to save data into Local Storage* How to save data into Session Storage* How to save data into a Web SQL database* How to save assets locally by using the Application Cache

#### Saving Data into Local Storage

`CAUTION`

When this proxy is used in a browser where Local Storage is not supported, the constructor will throw an error.

Every key/value pair in the Local Storage should have a unique key, to refer to its model data. Sencha Touch can generate a unique record id for you; you will only need to set an id on the proxy, which will be `prefixed` to the unique record id to make the Local Storage key.
In the FindACab app, you will use the LocalStorage proxy for saving user settings locally on the device. The Settings model in the FindACab app will contain a proxy like this:

```
proxy: {	type: 'localstorage', 	id: "Setting"}```
Note that the key/value pairs in the client proxies are strings! When you retrieve this data later from a store — by using, for example, the command Ext.getStore("MyStore").getAt(0) — it will decode this string to a JavaScript object for you. In other words, it serializes and deserializes data `automatically` when saving or retrieving it. Awesome!
An example Local Storage key/value pair for the FindACab app could look like this:
```
"Setting-51726e64-ae85-4fe8-9bea-aa085f499da6" : "{ 
	"id":"51726e64-ae85-4fe8-9bea-aa085f499da6", 
	"gps":null,
	"city":"Amsterdam",	"country":"NL" 
}"```
To generate an id with Sencha Touch, you need to implement an id strategy. There are three strategies you can use:

* `Ext.data.identifier.Sequential` is a sequential id generator. It generates ids in sequential order (1, 2, 3, etc.).* `Ext.data.identifier.Simple` is the default id generator in Sencha Touch. It generates ids in sequential order, prefixed with a prefix parameter. When no prefix parameter is set, it prefixes ids by default with ext-record- (e.g., ext- record-1, ext-record-2, ext-record-3, etc.).* `Ext.data.identifier.Uuid` is a UUID (Universally Unique IDentifier, also known as GUIDs (Globally Unique IDentifier generator). A UUID is a 128-bits id that can guarantee uniqueness across space and time. UUIDs were originally used in the Apollo Network Computing System and later in the Open Software Foundation’s (OSF) Distributed Computing Environment (DCE), and then in Microsoft Windows platforms.
To set an id strategy, you have to require one of the identifiers at the top of your model; for example: `requires: ["Ext.data.identifier.Uuid]`. The model should also have the idProperty set to the model field that can be used as an id, which defaults to the fieldname: id. (Make sure the field type matches!) In addition, the model should contain the identifier property. In this case, it can be set to uuid, which is why you are requiring this class at the top of the file.
Let’s take a look at the full code for the Settings model of the FindACab app, FindACab.model.Setting:
```
Ext.define('FindACab.model.Setting', {
        extend: 'Ext.data.Model',
        requires: ['Ext.data.identifier.Uuid'],
        config: {
            idProperty: 'id',
            identifier: 'uuid',
            fields: [{
                name: 'id',
                type: 'auto'
            }, {
                name: 'gps',
                type: 'boolean'
            }, {
                name: 'city',
                type: 'string'
            }, {
                name: 'country',
                type: 'string'
            }],
            validations: [{
                type: 'presence',
                field: 'city',
                message: "Please provide a city."
            }, {
	            type: 'presence',				field: 'country',				message: "Please provide a country."
            }],
            proxy: {
                type: 'localstorage',
                id: "Setting"
            }￼￼￼
        }```
#### Saving Data into Session Storage
```
proxy: {	type: "sessionstorage", 	id: "session-id-prefix"}```

#### Saving Data into a Web SQL Database for the FindACab App
`TIP`
Isn’t it annoying that you cannot delete a Web SQL database directly with the Google Chrome Dev Tools? Well, there is a solution to remove a database (and its tables). Just open the following URL in your Google Chrome browser: chrome://settings/cookies. Here you can search for any particular site or remove all locally stored data. In my case, I just search for localhost, and I get an exact overview of all my cookies, Local Storage, and Web SQL databases. I can double-click a database, and I’ll be prompted to remove the database. I click the Remove button, and it is gone!
#### Saving Assets Locally by Using the Application Cache
it is a technique for making your application available offline, without downloading the stylesheets, images, Sencha Touch framework, and custom classes. It will create a cached version of your mobile app by setting up a HTML5 cache manifest file, better known as the HTML5 AppCache.
AppCache is extremly handy for users who want to bookmark their mobile apps to their phone home screen. This is nice because the browser address bar will be gone, so it will give you a more native experience.
Whereas Web Storage saves key/value pairs in the browser’s Local or Session Storage, AppCache has the HTML5 ability to save (cache) a whole web app (the index.html file and all its assets, such as stylesheets, JavaScript, images, icons, fonts, etc.) in the browser’s cache to make it available, even if the client has no Internet connection at all.
The Application Cache manifest file is a simple text file that lists the resources that the browser should cache for offline access. Another reason for using AppCache is speed (because it caches and therefore loads faster) and performance (it reduces server load).
`TIP`
[Read the W3C spec about AppCache](http://www.w3.org/TR/offline-webapps/#offline). Also, take a look at [Jake Archibald’s blog post](http://alistapart.com/article/application-cache-is-a-douchebag) at A List Apart about troubleshooting AppCache.
Now how can you implement this? You don’t have to do much to make Application Cache work. First, you will have to edit the app.json file; it should define the appCache object. Let’s take a look:
```
"appCache": { 
	"cache": [		"index.html",		"resources/css/app.css"	],```The cache section lists all the items that need to be cached. Make sure that these filesexist, or else the caching will fail (and if it fails, you will not be happy):
```
"network": [ 
	"*"],```
The `network` section is basically used for (white) listing files that require an online connection. All requests to these resources `bypass the cache`, even if the browser is offline. Note you can also set this to a wildcard, *. This might be handy when the files from the cache section are cached but some assets are not listed and therefore missing. It will make sure that those unlisted assets will still be downloaded, no matter what. If I didn’t create a network section that points to the wildcard, then the application would miss these files:
```
	"fallback": [		//fall back items	￼
	]}//end appCache```
The fallback section in the app cache file will let us list fallback solutions for when a request fails. See, for example, the line `resources/images/resources/images/placeholder.gif`. If any image in the resources/images folder fails, it will display the placeholder.png file.

Then build the application with Sencha Cmd:

```
	sencha app build
```
Be aware that the Application Cache can be a pain sometimes for the following reasons:

* You cannot specify an expiration date/time for files in the cache. But you can expire the cache by making a change to the manifest file — for example, by adding a comment. Any change to the file causes all files to be recached. This is what Sencha does: it changes the Application Cache manifest by adding comments that represent generated unique keys.
* If any of the files listed on the manifest are not found, no caching will occur.
* While waiting for the manifest file, the browser will load the site `from the cache`. Therefore, changes to your cache manifest are acted upon only after a `second` refresh from when the file was modified.
* Manually clearing the browser cache from the Google Chrome Dev Tools does not force all files to be recached.

`TIP`

A manifest file must be served with the `MIME-typ`e text/cache-manifest. You may need to add this custom file type to your web server or to a .htaccess file: AddType text/cache-manifest .appcache.
