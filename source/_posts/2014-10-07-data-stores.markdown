---

layout: post
title: "Data Stores"
date: 2014-10-07 16:16:36 +0800
comments: true
categories: Sencha Touch
---
A data store is a mechanism to cache your data and is part of the Ext.data package. It is like a bucket full of data records. You can pick (select) a record out of this bucket (the data store) and add or remove records. Stores also provide functions for sorting, filtering, and grouping the model instances. You’ll need to give a model structure to the store with data. You can do this inline by setting the fields and data arrays (hardcoded), but a better MVC approach is to bind a model to the data store.

Sencha Touch has data-aware components — such as `lists, dataviews, and charts` — that need to be hooked up to a store in order to display data. I will discuss those in`Chapter 11.`In this chapter, you’ll learn:
* How to load data in a store* How to sort a data store locally How to sort data on a server* How to group a data store* How to filter a data store locally 
* How to filter a data store on a server 
* How to save/sync data in a store

#### Loading Data in a Store
For the FindACab app to be able to display the data, the data needs to be contained in the store. By default, when you create a store (and the data is not hardcoded), you will have to load the model data into your store.When autoLoad is not enabled, you have to manually load the store from your code, or from your developer’s console:

```
Ext.getStore('MyStore').load(this, records, successful, operation, eOpts);
```
`NOTE`

`Ext.getStore("MyStore")` is a lookup method; it finds a store (if the store is registered in the Ext.application() or controller) based on the store instance name or storeId through the StoreManager. Really, it’s a short alias for `Ext.data.StoreManager.lookup("myStore")`;.

We want the FindACab app to retrieve a list of cabs in the area. We already hooked up a proxy to the store, so we can load the data. When you run the Ext.getStore("Cabs").load() event in the console, it will look up the Cabs store through the StoreManager and return a store object with a data array that contains 20 items.

Instead of just loading the store, you can also handle a callback:

```
Ext.getStore('Cabs').load({
    callback: function(records, success, operation) {
        //callback function here
        console.log(records);
    },
    scope: this
});
```
The store has a callback function, which in this case logs all records after the store is loaded. You can also set a scope. In this case, when you log console.log(this) in your callback, it won’t log the scope within the callback, but rather the scope of the class where the store load() event is called.`TIP`
```
There are more events you can listen for in the store; for example, addrecords, beforeload, beforesync, refresh, removerecords, updaterecords, and write. Check the API docs for more details about the different store events.```
Now you will modify the store proxy config. Instead of entering a full proxy URL, you will retrieve the URL, the YELP_API key, and the YELP_TERM from the Utils.Commons static file, so it’s better organized. You can send parameters with the request by using the extraParams object, and you can modify these parameters from elsewhere in your code, as shown in Example 9-1.
```
Example 9 - 1.store / Cabs.js
Ext.define('FindACab.store.Cabs', {
    extend: 'Ext.data.Store',
    requires: ['Ext.data.proxy.JsonP'],
    config: {
        model: 'FindACab.model.Cab',
        autoLoad: false,
        proxy: {
            type: 'jsonp',
            url: Utils.Commons.YELP_API,
            noCache: false,
            extraParams: {
                term: Utils.Commons.YELP_TERM,
                ywsid: Utils.Commons.YELP_KEY,
                location: Utils.Commons.LOCATION
            },
            reader: {
                type: 'json',
                rootProperty: 'businesses',
            }
        },
    }
});```
In order to maintain the store callback in the controller, you will create a system event listener to listen to the store load event. For now, this code will only log the results, and show and hide a loading indicator. Example 9-2 shows the new FindACab.controller.CabController.
```
Example 9-2. controller/CabController.js
```
Ext.define('FindACab.controller.CabController', {
    extend: 'Ext.app.Controller',
    config: {
        models: ['Cab'],
        stores: ['Cabs']
    },
    init: function() {
        Ext.Viewport.mask({
            xtype: 'loadmask',
            message: 'loading...'
        });
        Ext.getStore('Cabs').load();
        Ext.getStore('Cabs').addListener('load',
            this.onCabsStoreLoad,
            this);
    },
    onCabsStoreLoad: function(records, success, operation) {
        console.log(records.getData());
        Ext.Viewport.unmask();
    }
});```
#### Sorting a Data Store Locally
After you retrieve data in your store, you might notice that the store is not sorted. It is possible to sort the records in a data store on the client side. You will use the `Ext.data.Store.sort(sorters, [defaultDirection], [where])` method, and you can pass in sorters_ object, which specifies the fieldname to sort and the direction, either ASC (ascending, A–Z) and DESC (descending, Z-A).
Here I construct a sorters array to sort the fieldname property by ASC:
```
sorters: [{	property: "fieldname", direction: "ASC"}]```
The sorters array or the sort() method on the store sorts the data collection inside thestore by one or more of its properties.
To programmatically sort a store from elsewhere in your code, you can pass in a singleargument, the fieldname to sort. This will toggle between ascending and descending:
```
Ext.getStore("Cabs").sort("name");```
Or you can pass in the full sorters configuration:
```
Ext.getStore("Cabs").sort({
    property: "fieldname",
    direction: "ASC"
});```Or just the string fieldname and strings "ASC" or "DESC":
```
Ext.getStore("Cabs").sort("name", "DESC");```
In the FindACab app, you will sort the Cabs list on the cab service name in alphabeticalorder. Therefore, the default sorter will be set to name:
```
sorters: [{
    property: "name",
    direction: "ASC"
}],
```
It is possible to add sorters on top of each other. For example, first sort on the field name and afterward filter on the field distance. You do so by passing an array:

```
store.sort([{
    property: 'name',
    direction: 'ASC'
}, {
    property: 'distance',
    direction: 'DESC'
}]);
```
When the sort() method is called again with a new sorter object, any existing sorters will be removed. When the sort() method is called without any arguments, the existing sorters will be reapplied. To keep existing sorters and add new ones, you will need to run the add() method on the sorters object. Here is how you can add sorters to a store:

```
store.sorters.add(new Ext.util.Sorter({
    property: 'phone',
    direction: 'ASC'
}));
store.sort();
```
The previous examples make sense when you want to sort on local stores. However, it’s also possible to sort remotely on the server side.

#### Sorting Data on a Server
The data that you retrieve from the server side might be very large. It could be faster to sort it on the server side instead of locally. Luckily, Sencha Touch provides a way to implement server-side paging, called `remote sorting`. You will use the Ext.data.Store.remoteSort boolean, and you will use the sorters object directly in the store or run the Ext.data.Store.sort(sorters, [defaultDirection], [where]) method where you can pass in a sorters object. (See the previous section on how to sort a store.)

If you want to enable remote sorting, set the following settings in the store class definition:

* A pageSize to define the number of records that constitutes a “page.” (Note that the default page size is set to 25.)* The boolean remoteSort config in the store class definition to true. (Note that remote sorting is false by default.)* The sorters object, as described in the previous section:
```
pageSize: 30, 
remoteSort: true, 
sorters: [{	property: "fieldname",	direction: "ASC" }]```
Unfortunately, because we do not have control over the Yelp server side, we won’t implement a remote sorter for the FindACab app. However, I do want to share a running example of a remote sorter. In this demo, there is another data store with Car objects that sorts cars by brand in ascending order:
```
Ext.define('RemoteTest.store.Cars', {
        extend: 'Ext.data.Store',
        requires: ['Ext.data.proxy.JsonP'],
        config: {
            model: 'RemoteTest.model.Car',
            autoLoad: true,
            remoteSort: true, // 
            sorters: [{ //
	            property: "brand",
	            direction: "ASC"
	        }],
	    pageSize: 20, //
	    proxy: { //
	        type: 'jsonp',
	        url: 'http://someurl.com/test.php',
	        reader: { //
	            rootProperty: 'results',
	            totalProperty: 'total',
	            successProperty: 'success'
	        }
	    },
	}
});```
`WARNING`

The previous store has an autoLoad property. This makes sense for demo purposes, but in a real application, you would probably want to programmatically sort and load the store.

Currently, this Car store doesn’t do much. That’s because there is no server side implemented. This is OK, because the server-side code can be a black box for us. However, let’s assume that http://<someurl>.com/test.php is a working web service that sends Car objects back.
In the Google Developer network tab, you can see a request is made, which sends the following GET request to your server:

```
http://someurl.com/test.php?_dc=1386924081041&page=1&start=0&limit=20 &sort=%5B%7B%22property%22%3A%22brand%22%2C%22direction%22%3A%22ASC%22%7D%5D &callback=Ext.data.JsonP.callback1
```
Let’s format the query string parameters:

```
page:1start:0limit:20 sort:[{"property":"name","direction":"ASC"}]
```
The limit parameter comes from the store pageSize. The page and start parameters are used for paging. On the server side, you can calculate which set of items you have to send back to the client-side code.

The http://<someurl>.com/test.php web service requires some logic to sort the data (e.g., in a database) and send the correct set of data back.

The server response for sending back Car objects (in PHP) could look like Example 9- 3. The names of the success, total, and results properties should be set in the store’s reader.```
{
    "success": true,
    "total": 500,
    ￼￼￼￼￼
        "results": [{
                "id": 1,
                "brand": "BMW",
                "type": 7
            }, {
                "id": 2,
                "brand": "Mercedes",
                "type": 5
            }
            ... //20 results in total
    ]
}```
#### Grouping a Data Store
Grouping a data store makes sense when you want to display data into an Ext.List component in Sencha Touch and you want to visually group data. For example, when you have a store with companies, you could, for example, group by “city.” This will list every company per city.
To enable grouping in a store, implement the groupField and groupDir configurations directly in the store class definition. The groupFields sets the field to group and the groupDir sets the direction (ASC or DESC):

```groupField: '<model-field-name>', groupDir: 'ASC' //or DESC
```
To dynamically group a store, you can run the setGrouper() method on a store object:
```
Ext.getStore('Cabs').setGrouper({
    direction: 'ASC', //or DESC 
    property: '<model-field-name>'
});```
You will implement grouping on the Cabs store for the FindACab app list. This time, you will not group on city, because all the data that is in the Cabs store already shares the same city — for example, Amsterdam. Therefore, let’s group on the first alphabetical character of a cab service name. (See Figure 9-1.) You would see a group “A” that lists all names that start with an A, a group “B” that lists all names that start with a B, and so on. It’s the same behavior as when you open the contacts list on an iPhone. Names are grouped, and if you want, you can even display an index bar on the side to quickly browse to the corresponding character.
{% img /images/2014/10/figure7-3.png%}
To achieve this, you will need the Ext.data.Store.grouper object, with a custom group function: groupFn(). You can set the grouper object directly in the store class definition, as shown in Example 9-4.
```
grouper: {
    groupFn: function(record) {
        return record.get('name')[0].toUpperCase();
    }
}```
The groupFn function with the code return record.get("name") [0].toUpperCase(); will group the data in the store on the first (uppercase) character of the name field.
#### Filtering a Data Store Locally
A data store can also filter records. When a filter is applied, the data store will not remove records. The same records are still available in the store, but only the records that match the filter criteria are displayed.

Filters are added as arrays. Here’s how to implement a filter array directly in the store class definition:

```
filters: [{
    property: "fieldname",
    value: "match"
}],
```
You can also filter programmatically. Just run the following method from a store instance:```
Ext.data.Store.filter(filters, [value], [anyMatch], [caseSensitive]);```It’s possible to filter on the first characters of a field or from anywhere (argument: anyMatch), and it’s also possible to filter for case sensitivity (argument: `caseSensitive`).
#### Custom Filter Functions
You can also create custom filter functions. To do so, you can set a filterFn in thearray or use the filterBy(fn) method on the store.
```
filters: [{
    filterFn: function(item) {
        return item.get("phone").length > 0;
    }
}],```
#### Stacking Filters
o add filters on top of each other — for example, to filter on a name with a value of Taxi and filter on a distance of 20 miles — you pass in an array. Here, I stack a couple of filters on top of each other:
```
store.filter([{
    property: "name",
    value: "Taxi"
}, {
    property: "distance",
    value: "20"
}]);```
`Note`
Instead of passing an array with filter objects into the filter() method, I could call the filter() method again without the filter objects as arguments. Unlike sorters, filters won’t reset if you call them again. When you want to renew the filter, you have to clear it first:
```
store.clearFilter();```
#### Filtering Data on a Server
The data that you retrieve from the server side might be very large. It could be faster to filter it on the server side instead of locally. Luckily, Sencha Touch provides a way to implement server-side filtering.
You will use the remoteFilter boolean and the array with filters. Here’s an example of the store class definition:
```
remoteFilter: true,
filters: [{
    property: "fieldname",
    value: "match"
}],```
because we do not have server-side control over Yelp.com, we won’t implement a remote filter for the FindACab app. I do want to share an example of a remote filter, however. In this demo, there is another data store with Car objects and with remote filtering enabled. It has a filter set that filters on car brand:
```
Ext.define('RemoteTest.store.Cars', {
    extend: 'Ext.data.Store',
    requires: ['Ext.data.proxy.JsonP'],
    config: {
        model: 'RemoteTest.model.Car',
        autoLoad: true,
        pageSize: 20,
        remoteFilter: true,
        filters: [{
            property: "brand",
            value: "BMW"
        }],
        proxy: {
            type: 'jsonp',
            url: 'http://someurl.com/test.php',
            reader: {
                rootProperty: 'results',
                totalProperty: 'total',
                successProperty: 'success'
            }
        },
    }
});```
`WARNING`

When the remoteFilter configuration has been set to true, you will have to manually call the load method after every filter you set to retrieve the filtered data from the server.

Let’s assume that http://<someurl>.com/test.php is a working web service that sends
Car objects back. We’ll filter on a car brand of BMW, 20 per time (page).In the Google Developer Network tab, you can see a request is made that sends thefollowing GET request to your server:
```
http://someurl.com/test.php?_dc=1387182737587&page=1&start=0&limit=20 &filter=%5B%7B%22property%22%3A%22brand%22%2C%22value%22%3A%22BMW%22%7D%5D &callback=Ext.data.JsonP.callback1```
Let’s format the query string parameters:
```
page: 1
start: 0
limit: 20
filter: [{
    "property": "brand",
    "value": "BMW"
}]```
As you might have noticed, the implementation and server requests of a remote filter are similar to the implementation and server requests of a remote sorter. The limit parameter comes from the store pageSize. The page and start parameters are used for paging. On the server side, you can calculate which set of items you have to send back to the client-side code.

The http://<someurl>.com/test.php requires some logic to filter their data (e.g., in a database), and send the correct set of data back.

The server response for sending back Car objects (in PHP) could look like Example 9- 6. The names of the success, total, and results properties should be set in the store’s reader.

```
// Example 9-6. A server response in PHP
{
    "success": true,
    "total": 500,
    "results": [{
            "id": 1,
            "brand": "BMW",
            "type": 7
        }, {
            "id": 2,
            "brand": "BMW",
            "type": 5
        }
        ...
    ]
}
```
#### Syncing Data in a Store

To save/synchronize records in a data store with the server, you will have to run the `sync()` method on the store. It’s also possible to automatically sync the store with the server side. That way, the remote server keeps in close sync with your Sencha Touch app. You can enable this by setting the `autoSync` property to `true`.

More likely, you will use the Ext.data.Store.sync() method to synchronize the store with its proxy programmatically:

```
store.sync(options);
```
Before the sync process, Sencha Touch will fire a `beforesync` system event. When you run the sync() method, all inserts, updates, and deletes are sent as three separate requests, and if you want, you can declare the order in which the three operations should occur. After the sync process, an object is returned with the child objects `added, updated, and removed`.
