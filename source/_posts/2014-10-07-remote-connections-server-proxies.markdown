---
layout: post
title: "Remote Connections (Server Proxies)"
date: 2014-10-07 11:09:16 +0800
comments: true
categories: Sencha Touch
---
Models and stores can load and save data via a so-called `proxy`. Sencha Touch has two main types of proxies: `client proxies` and `server proxies`. Client proxies save and load their data locally. Server proxies interact with a remote server.You can read more about client proxies and saving data offline (with techniques such as Local Storage, Session Storage, Web SQL, and AppCache) in Chapter 10. In this chapter, I will talk about the different kinds of server proxies.

A server proxy communicates by sending requests to some remote server. There are four types of server proxies you can use out of the box:

* Ext.data.proxy.Ajax

	Sends a request to the server on the `same domain` by using — you guessed it — AJAX.
* Ext.data.proxy.JsonP
	
	Sends a request to a server on a different domain by using JSON with padding (JSONP).
* Ext.data.proxy.Rest

	A kind of AJAX proxy that automatically maps to four readable actions, the RESTful HTTP verbs: create, read, update, and destroy.
* Ext.data.proxy.Direct
	Uses the Ext.Direct technology (originally created for Ext JS) to remote server-side methods to the client side. Ext.Direct allows communication between the client side of a Sencha app and all popular server platforms.

Let’s implement the two most used server proxies for receiving data: the AJAX proxy and the JSONP proxy. We’ll also discuss how to communicate with a server without using a proxy, by making AJAX and JSONP requests.
For the FindACab app, you will implement a JSONP proxy to retrieve TaxiServices data for a certain location from an external web server (Yelp.com).
In this chapter, you’ll learn about:
* Saving or retrieving data from the same domain with AJAX
	How to implement AJAX proxies 
	How to implement an AJAX request* Retrieving data from an external domain with JSONP
	How to implement JSONP proxies 
	How to implement a JSONP request

* Saving or retrieving data from an external domain with AJAX
	How to implement `CORS`
#### Saving or Retrieving Data from the Same Domain with AJAX
##### Implementing AJAX Proxies
At the top of your model or store class, you will require Ext.data.proxy.Ajax, so the Ext.Loader knows to load the AJAX proxy framework class first. Next, you will create a proxy object:```
config: {	//model or store configs here	proxy: {		type: 'ajax',		url: 'data/data.json' 	}}```
The proxy object can be set into the config object from a store or a model

The URL can point to a web service URL, or it can just point to some file `locally`. In Example 8-1, an AJAX proxy was used to load the local file, /data/data.json, which is in the same web folder as the app folder.

You can set a proxy object and it will load the data. To let the proxy understand (read) the data, you can set a reader object (Ext.data.reader.Reader).

```
proxy: {	type: 'ajax',	url: 'data/data.json', 	reader: {		type: 'json',		rootProperty: 'results' 	}}
```
Note the steps to set up the reader object:

* First, set up a type. There are two types of data responses that the reader can interpret: json and xml.
* Define the rootProperty. It points to the root node of your data, from where it has to start reading the data.
There are more configs you can set in Ext.data.reader.Reader. The ones I use regularly are:
* successProperty
	Should point to the response property with a success attribute (by default, it points to the property name success).
* messageProperty
	Should point to the response property with a response message (this property is optional).
* totalProperty

	Should point to the response property with a total number of records in the dataset. This is required only while paging, when the whole dataset is not passed in one go, but is being paged from the remote server (by default, it points to the property name total).
Let’s take a look at the FindACab app in Example 8-1. Here I retrieve data from the same domain into the store by setting an AJAX proxy into the data store.
```
Ext.define('FindACab.store.Cabs', {
    extend: 'Ext.data.Store',
    requires: ['Ext.data.proxy.Ajax'],
    config: {
        model: 'FindACab.model.Cab',
        autoLoad: true,
        proxy: {
	        type: "ajax",
	        url : "data/data.json",
	        reader: {
	            type: "json",
	            rootProperty: "businesses"
	        }
	    },
    }
});```
Sometimes when you want to retrieve data from an external server, the response data is just not what you expected. For example, you want to reorder, cache, or rename properties, or you just want to add more data or maybe you don’t have control over the remote server to provide JSONP. In these cases, an AJAX proxy can be really handy. You can create an own “proxy” on the server side that connects to the external server and fetches the data. When it is retrieved on the server side, you modify the response. This custom proxy script is running on the same server as your app does, so you can hook it up with a Sencha Touch AJAX proxy back to the Sencha Touch data store. In PHP, such a scenario might look like this:

```
<? php

function URL_get_contents($URL) {
    if (!function_exists('cURL_init')) {
        die('CURL is not installed!');
    }
    $ch = cURL_init();
    cURL_setopt($ch, CURLOPT_URL, $URL);
    cURL_setopt($ch, CURLOPT_RETURNTRANSFER, true);
    $output = cURL_exec($ch);
    $httpCode = cURL_getinfo($ch, CURLINFO_HTTP_CODE); //get the code of request
    cURL_close($ch);
    if ($httpCode == 400) return 'Bummer';
    if ($httpCode == 200) {
        $data = json_decode($output); // do some fancy stuff here $data = json_encode($data);
        header('Content-Type: application/json');
        echo $data;
    }
} 
?>
```
Retrieving data through AJAX proxies can be handy. The data is directly in your store, which is great for the data-bind components that connect to a store, such as the `Ext.List`
Sometimes you don’t need to retrieve data through a proxy. Maybe you don’t want to display the data results into a data-aware component, or you just don’t need a model or a store. Other times, the proxy is already in use by a client proxy (you can set just one proxy per model or store),Although in such a scenario you could also switch proxies with setProxy on the model or store, you can also choose to make plain AJAX requests. I will discuss this option next.

#### Implementing an AJAX Request
In the previous section, we retrieved data in a store via an AJAX proxy. Sometimes you don’t need to save or retrieve data in a store, maybe because you are not displaying the data in a data-aware component, such a as an XTemplate,n that case, a simple Ext.Ajax request, with parameters to pass in, might be an easier solution for saving and retrieving data on the same domain than hooking up an AJAX proxy to a store.

At the top of your class you will require Ext.Ajax, so the Ext.Loader knows to load the AJAX framework class first. Next you will create the request:```
Ext.Ajax.request({
            url: 'somescript.php',
            timeout: 60000,
            method: 'POST',
            params: {
                location: 'Amsterdam NL'
            },
            success: function(response){
                //remove loading mask
                Ext.Viewport.unmask();
                try {
                    var text = response.responseText;
                    var results = Ext.JSON.decode(text);
                    console.log(results.businesses);
                } catch (e) {
                    //you can never assume that data is the way you want.
                    console.error(e);
                }
            },
            failure: function(e){
                //remove loading mask
                Ext.Viewport.unmask();
                console.error(e);
            }
        });```Ext.Ajax is a singleton instance of an Ext.data.Connection, which is the class that is used to communicate with server-side code.
`XHR2, NEW IN SENCHA TOUCH 2.3`
A new config setting, released in Sencha Touch 2.3, is to set up your AJAX request through the XHR2 (AJAX2) object. You will just need to enable the boolean property xhr2:true in the request object. XMLHttpRequest Level 2 is like XHR but with a huge makeover that allows you to do things like uploading progress events (Sencha Touch 2.3 also has a new progressbar component, Ext.ProgressIndicator!) and support for uploading/downloading binary data.Check out “[New Tricks in XMLHttpRequest2](http://bit.ly/xmlhttprequest2)” and [“XHR2 Uploads and Downloads”](http://docs.sencha.com/touch/2.3.1/#!/guide/xhr2_guide) to get more information about XHR2.
Sometimes you don’t have access to the source — for example, because the script is hosted on an external website. In the case of the FindACab app, we want it to receive data from Yelp.com. The next part discusses the tricks to retrieve data from an external domain.
#### Retrieving Data from an External Domain with JSONP
While AJAX in general cannot send and retrieve data from an external domain because of the cross-domain policy, JSONP can send and retrieve data from an external domain. It `only` uses HTTP `GET` and therefore cannot use HTTP `POST` to post (large) data to an external domain
In other words, you can send data, but it’s sent in the query string rather than the request body,sending data through GET is less secure than over POST because the data that will be sent is part of the URL. So it’s visible in the `browser history` or in `server logfiles`.
It is impossible to request or send data to external domains because of the same-origin policy; the browser ignores any connection, assuming nobody else is allowed. JSONP works differently; it’s a little like a magic trick — one that is well known.
Under the hood, it works as follows (again, see Figure 8-2):
{% img /images/2014/10/figure8-2.png%}
#### Implementing JSONP Proxies
The JSONP proxy works only when the server supports JSONP or when you have control over the server. Also, this trick can only send and retrieve data via GET requests.```
proxy: {
    type: 'jsonp',
    URL: 'http://externaldomain.com/users',
    reader: {
        type: 'json',
        rootProperty: 'results'
    }
}```
#### Implementing the JSONP Proxy for the FindACab App
Did you notice that the fields of the business object in the previous JSON response have the same names as the fields from the Sencha FindACab.model.Cab model? These fields map to the model fields, so you can later use them in your code. What if the JSON response has a slightly different name than the name that is defined in the model? You can either change the model field name, or you can add a mapping property to the model field.
For example, when you have a complex data feed that looks like this (note the
car:brand: notation):```
{
    results: [
        car: brand: [{
            name: "BMW",
            series: 7
        }, {
            name: "BMW",
            series: 5
        }]
        ...
    }```
You can add a mapping attribute to the field. In this case, brand maps to `["car:brand"][0].name:`

```
Ext.define("MyApp.model.Car", {
    extend: "Ext.data.Model",
    config: {
        fields: [{
            name: "brand"
            mapping: "['car:brand'][0].name"
        }]
    }
});
```JSONP request
```
Ext.define('FindACab.store.Cabs', {
    extend: 'Ext.data.Store',
    requires: ['Ext.data.proxy.JsonP'],
    config: {
        model: 'FindACab.model.Cab',
        autoLoad: true,
        proxy: {
            type: 'jsonp',
            noCache: false,
            url: FindACab.utils.Commons.Commons.YELP_API,
            extraParams: {
                term: FindACab.utils.Commons.YELP_TERM,
                ywsid: FindACab.utils.Commons.YELP_KEY,
                location: FindACab.utils.Commons.LOCATION
            },
            reader: {
                type: 'json',
                rootProperty: 'businesses',
            }
        },
    }
});```
Instead of using the proxy for a JSONP request, you can also use the Ext.data.JsonP method, which you will do in the next section.

#### Implementing a JSONP Request
View components — like lists, dataviews, or charts — are data-aware. You need to hook up a data store to preview the view with data. The previous section explained how to `retrieve` external data from Yelp in a data store. Later, this data will be displayed in an `Ext.dataview.List` view component,When retrieving external data, sometimes you don’t need to retrieve data in a store — for example, when you’re displaying data in an `Ext.XTemplate` (or another component that isn’t data-aware) -- because you want to have the control over the HTML and styling of the component.In that case, a simple Ext.JsonP request, with parameters to pass in, might be an easier solution for retrieving data from an external domain than retrieving it through a JSONP proxy.

```
Ext.data.JsonP.request({
            url: 'somescript.php',
            callbackKey: 'callback',
            params: {
                location: "Amsterdam NL"
            },
            success: function(response, request){
                //remove loading mask
            },
            failure: function(e){
                //remove loading mask
            }
        });
```
#### Saving or Retrieving Data from an External Domain with AJAX
You want to post (or get) data from an external domain, but posting data is only possible with AJAX, not with JSONP. JSONP cannot send data with HTTP POST, and AJAX in general cannot retrieve and post data from external domains so you might run into a problem. What you actually want is an AJAX request to save and retrieve data to an external domain. Read on for the trick to this.
##### Implementing CORS
You will need to set up Access-Control-Allow HTTP headers on the web server to post (and retrieve) data with AJAX to an external URL.
Sencha Touch provides additional configuration. In an Ext.Ajax.request or AJAX proxy, you have to set the withCredentials boolean to true and set the useDefaultXhrHeader boolean to false to not send the default Xhr header with every request.

Sencha Touch provides additional configuration. In an Ext.Ajax.request or AJAX proxy, you have to set the withCredentials boolean to true and set the useDefaultXhrHeader boolean to false to not send the default Xhr header with every request.
The Ext.Ajax() method (and also the model.save() and form.submit() methods) transmit through the XMLHttpRequest() object, and therefore you can run into security restrictions — cross-domain restrictions — when posting to a different domain. However, there is a way to post data to another server with AJAX; you need to set up your server so it’s possible to set up the HTTP headers (Access-Control-Allow headers) on the web server for that. This technique is called cross-origin resource sharing (CORS), and it enables you to override the default restrictions that prevent a web server from making AJAX requests to domains other than where the app is launched. CORS is supported by the modern browsers.
An example of such an HTTP header might look like:
```
Access-Control-Allow-Origin: *```