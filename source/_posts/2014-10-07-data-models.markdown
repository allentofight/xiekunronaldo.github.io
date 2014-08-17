---
layout: post
title: "Data Models"
date: 2014-10-07 08:49:56 +0800
comments: true
categories: Sencha Touch
---
Every field can be given a data type,When no datatype is specified, the datatype will be set to auto, which means there is no datatype conversion at all. This might be a solution for complex datatypes such as objects.
Models may contain (optional) validations and associations (relations to other models). Both models and stores can have a proxy to send and retrieve data in JSON, XML, or JavaScript array notation.
本章，我们将学习以下知识点
* How to validate a model* How to save a model to the server 
* How to implement a model association

#### Validating a Model
In the model config object, you can set a validations array. This array contains validation objects:

```
validations: [
	{type:'presence', field: 'age'},
	{type:'length', field:'name', min: 2},
	{type:'inclusion', field:'gender', list:['Male', 'Female']},
	{type:'exclusion, field:'username', list:['Admin', 'Operator']},
	{'format', field:'username', matcher: /([a-z]+)[0-9]{2,3}/}
]
```
As you can see, there are different validation types. For example, you can validate fields on presence (if the data exists), on length (if a string has a min or max length of characters), inclusion (if the data string exists in a given list array), exclusion (if the data string does not exist in a given list array), and on format (if the data matches a regular expression, matcher).

Let’s test this. Open the FindACab app in your modern browser. In your debug console, you can enter the code for creating a new model instance:

```
var setting = Ext.create('FindACab.model.Setting', { city: "Amsterdam"});
```
Now you can validate your record with the following command in your console:

```
setting.validate();
```
#### Saving a Model to the Server
We will use a simple Car model that looks like this:

```
Ext.define('SaveTest.model.Car', {
	extend: 'Ext.data.Model',

	config: {
		fields: [
			{ name: 'id', type: 'int'},
			{ name: 'brand'},
		],
		proxy: {
            type: 'rest',
            //url : '/cars',
		    format: 'php', 
		    api: {
		        create: 'cars/addcar',
		        update: 'cars/editcar',
		        read: 'cars/loadcar',
		        destroy: 'cars/deletecar'
		    }	
        }
	}
});
```
接下来做一些测试

```
    var car = Ext.create('SaveTest.model.Car', {
        brand: 'Mercedes'
    });

    //save = POST method
    car.save({
        success: function() {
            console.log('The car record is saved');
        },
        failure: function(){
            console.log('The car record could not be saved.');
        }
    });
```
Once you’ve created a record, you can save it to the server side with record.save():

```
car.save({
```

In order to load data from the server side via the model, you can use record.load(id, callback). When there is no store available, the trick is to first request the model via the ModelManager:

```
Ext.ModelManager.getModel('SaveTest.model.Car').load(1, { 
	success: function(car) {		console.log("Load Car: " + car.getId()); },	failure: function(){		console.log("The car could not be loaded");	} });
```
`Note`

```
When you set the proxy type to ajax instead of rest, you can still use readable URLs. The only
difference is the HTTP method. Adding, editing, and removing records will be an HTTP POST call.Loading records will be an HTTP GET call.
```
#### Cross-Domain Restrictions
The record.save() method (as well as the Ext.Ajax and form.submit() methods) transmit through the `XMLHttpRequest()` object, and therefore you might face security restrictions when posting to a different domain. These are known as `cross-domain restrictions`.

However, there is a way to save and post data to another server. It requires setting up special HTTP headers (`Access-Control-Allow headers`) on the web server. This technique is called `CORS` (cross-origin resource sharing), and it enables you to override the default restrictions that prevent a web server from making AJAX request to other domains. For more information, see Chapter 8 and the AJAX sections of this book, or check out the HTML5 Rocks website.

Models can also have relationships with other models. For example, a User model can have a relationship with multiple Order models, and one Order model can have just one relationship with one payment method.

#### Implementing a Model Association
Models can have relationships with one or more other models. The following model associations can request data through each other:

* Ext.data.association.HasOne 
* Ext.data.association.HasMany 
* Ext.data.association.BelongsTo
You can create two or more models and connect these to each other with the hasMany, hasOne, and belongsTo configs:```
Ext.define('TaxiService', { 
	extend: 'Ext.data.Model',	config: {		fields: ['id', 'name'], 		hasMany : {			model: 'Car', name: 'cars' }	} 
});
Ext.define('Car', {	extend: 'Ext.data.Model',	config: {		fields: ['id', 'brand']	} });```
When you want to use associations, you have to add these to the requires array at the top of your model class:```
requires: [ 
	'Ext.data.association.HasOne', 
	'Ext.data.association.BelongsTo', 
	'Ext.data.association.HasMany']```{% img /images/2014/10/figure7-3.png%}
When you want to display the different brands of Cars per TaxiService, you could express the relationships between both models. One TaxiService may contain many different types of Car records. You can define this relationship by specifying the hasMany property into the TaxiService model, so later you can request Car information via the TaxiService model.
You will need a foreignKey on the owner model that links to the associated model. By default, it takes the lowercase name of the owner model plus _id. For example, the TaxiService model would create taxiservice_id as the foreign key.
{% img /images/2014/10/figure7-4.png%}
```
//Example 7-2
Ext.define('AssociationsTest.model.TaxiService', {
    extend: 'Ext.data.Model',

    requires: ['Ext.data.association.HasMany'],

    config: {
        fields: ['id', 'name'],
        hasMany  : { //<1>
        	model: 'AssociationsTest.model.Car', //<2>
        	name: 'cars', //<3>
        	foreignKey: 'taxiservice_id' //<4>
        }
    }
});```
As with every relationship, their connection can be confirmed; the Car model answers its relationship by confirming it belongsTo the TaxiService model. This belongsTo association allows me to request the TaxiService information via the Car model.
```//Example 7-3. model/Car.js
Ext.define('AssociationsTest.model.Car', {
    extend: 'Ext.data.Model',
    requires: ['Ext.data.association.BelongsTo'],
    config: {
        fields: [{
            name: 'id', //<1>
            type: 'int'
        }, {
            name: 'brand'
        }, {
            name: 'taxiservice_id',
            type: 'int' //<2>
        }],
        belongsTo: {
            model: 'AssociationsTest.model.TaxiService'
        }
    }
});
```
And now comes the fancy stuff: requesting all the records that have a relationship through the parent model. As covered in Chapter 6, many records are saved in a store — the set of all records together. When specifying the hasMany association, you will point to the relating hasMany model and set a name (cars). The association will dynamically create a `virtual store` and the name that is set in the association will become the store name. You can call this store by running the `cars()` method on the TaxiService model. Remember, creating a store will not autoload the data, so you will need to `load()` the virtual store manually.
Let’s take a look at the next code snippets. First, create two TaxiService records, each of which will contain Car records. A TaxiService model has a hasMany relationship with the Car model:

```
var taxiAmsterdam = Ext.create('AssociationsTest.model.TaxiService', { 
	id: 1,	name: 'Taxi Amsterdam' 
});var yellowCab = Ext.create('AssociationsTest.model.TaxiService', { 	id: 2,	name: 'Yellow Cab' 
});
```
The Car records know that they belong to the TaxiService parent model (see previous code) because of the `belongsTo` relation (in Example 7-3). It has a property, `model`, that points (belongs) to the `TaxiService` model. The TaxiService model has a hasMany relationship to the Car model and it has a property: foreignKey (again, see Example 7- 2).

Now let’s create some Car records:

```
var bmw = Ext.create('AssociationsTest.model.Car', { 
	id: 1,	brand: 'BMW' 
});
var mercedes = Ext.create('AssociationsTest.model.Car', { 	id: 2,	brand: 'Mercedes' 
});
var vw = Ext.create('AssociationsTest.model.Car', { 	id: 3,	brand: 'Volkswagen' 
});
```
To get access to all the Car records from the TaxiService record, you can call the cars() method to create a virtual store:

```
var taxiAmsterdamStore = taxiAmsterdam.cars(); 
var yellowCabStore = yellowCab.cars();
```
The add() and sync() lines in the next piece of code just add the particular Car records to each TaxiService store. You will read more about stores in the next two chapters:

```
//add new cars to the StoretaxiAmsterdamStore.add(mercedes); taxiAmsterdamStore.add(vw);
taxiAmsterdamStore.sync(); 

yellowCabStore.add(bmw); 
yellowCabStore.sync();
```
To request the TaxiService data from a Car record (and to test the belongsTo relation), you can use the autogenerated getter getTaxiService(). Now, let’s request the TaxiService data for the first record in the taxiAmsterdamStore:

```
console.log(taxiAmsterdam.cars().first().getTaxiService());
```
#### Remote Associations
You can also request Car records from the server side. To do so, you would need to add a remote proxy to the Car model:

```
proxy: {	type: 'ajax',	reader: {	rootProperty: 'cars'	},	url: 'app/data.php' 
}
```
After you add the records and sync the taxiAmsterdamStore and yellowCabStore, this proxy will `POST` the messages shown in Examples 7-4 and 7-5 to `app/data.json`.

```
Example 7-4. taxiAmsterdam{cars: [{id:1, brand:BMW, taxiservice_id:1}, {id:2, brand:BMW, taxiservice_id:1}]} 
Example 7-5. yellowCab{cars: [{id:1, brand:BMW, taxiservice_id:2}]}
```
On your server side, you could code a script that takes these objects and adds them to adatabase.
When you want to load data from the remote, you can run the following command:
```
taxiAmsterdamStore.load(function(records){ 
	console.log(records);});```
Here, you are loading data into the taxiAmsterdamStore, which has a model with associations hooked up. Therefore, it will fire a GET request, with the following parameters:```
filter:[{"property":"taxiservice_id", "value": 1}]````TIP`
```
When the remote store is loaded, the store is automatically filtered so that only records with a matching foreign key are included in the resulting child store. You can override this by specifying the filterProperty.```
On your server side, you could code a script that filters for the taxiservice_id with the value set to 1. The data returned for this request from the server might look like the following example, which is written in PHP:

```
<? php
//Return response as JavaScript header('Content-Type: application/javascript'); echo '{
"cars": [{
    "id": 1,
    "brand": "BMW",
    "taxiservice_id": 1
}, {
    "id": 2,
    "brand": "Mercedes",
    "taxiservice_id": 1
}]
}'; 
?>
```
The FindACab app will not display the different brands of cars, which is why you won’t implement any model associations. That is because you will use the Yelp API to provide data about taxi businesses. It will not supply information about car brands like the previous examples did.

`TIP`

If you want to read more about associations, check out [Rob Boerman’s great tutorial](http://appointsolutions.com/2012/07/using-model-associations-in-sencha-touch-2-and-ext-js-4/) and[MiamiCoder’s tutorial](http://miamicoder.com/2012/sencha-touch-2-models-hasmany-associations-php-example/)with PHP server-side code

#### Summary
The Sencha data package contains the three key classes `Ext.data.Model`, `Ext.data.Record`, and `Ext.data.Store`.
