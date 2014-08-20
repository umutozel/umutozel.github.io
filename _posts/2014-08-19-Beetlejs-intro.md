---
layout: post
title: Beetle.js - Introduction
comments: true
redirect_from: "/2014/08/19/Beetlejs-intro/"
permalink: beetlejs-intro
---

![Beetle.js logo placeholder](/assets/beetlejs_logo_540x252.png)

[Beetle.js](http://beetlejs.com/ "Beetle.js") is a data manager for Javascript.
The goal is to be able to work with data as easy as Entity Framework and LINQ.

##Features
* Tracks objects and persists changes to server
* Can work with Knockout and Angular objects (Backbone soon, and others can be implemented easily)
* Can work with Q and jQuery promises
* Supports data model inheritance
* Supports aggregate functions
* Can work without metadata
* Can work with Mvc and WebApi Controllers
* Supports property, entity validations
* Can use existing data annotation validations (carries multi-language resource messages to client)
* Can query server with Http POST
* Can be extended to support almost every library (client and server side), flexible architecture
* Auto fix navigation properties (after foreign key set, entity attach etc..)
* Can check-auto convert values for its proper data types
* Can be internationalized (for internal messages, validation messages etc..)

##Current prerequisities
All dependencies have base types so custom implementations can be made easily.

* Entity Framework
* WebApi or Asp.Net Mvc project for service
* Knockout.js or EcmaScript5 Properties (for Angular) for providing observable objects
* JQuery for ajax operations

##Usage
* Create a Controller and inherit from BeetleApiController, generic argument tells we are using Entity Framework context handler with TestEntities context (DbContext)
{% highlight cs %}
public class BeetleTestController : BeetleApiController<EFContextHandler<TestEntities>> {
		
	[HttpGet]
	public IQueryable<Entity> Entities() {
		return ContextHandler.Context.Entities;
	}
}
{% endhighlight %}
* Configure routing
{% highlight cs %}
public static class BeetleWebApiConfig {

	public static void RegisterBeetlePreStart() {
		GlobalConfiguration.Configuration.Routes.MapHttpRoute("BeetleApi", "api/{controller}/{action}");
	}
}
{% endhighlight %}
* Create an entity manager
{% highlight javascript %}
var manager = new EntityManager('api/BeetleTest');
{% endhighlight %}
* Create a query
{% highlight javascript %}
var query = manager.createQuery('Entities').where('e => e.Name != null');
{% endhighlight %}
* Execute the query and edit the data
{% highlight javascript %}
manager.executeQuery(query)
	.then(function (data) {
		self.entities = data;
        data[0].UserNameCreate = 'Test Name';
    })
{% endhighlight %}
* Execute local query
{% highlight javascript %}
var hasCanceled = self.entities.asQueryable().any('e => e.IsCanceled == true').execute();
// q is shortcut for asQueryable, x is shortcut for execute
var hasDeleted = self.entities.q().any('e => e.IsDeleted').x();
// alias is optional
var hasDeleted = self.entities.q().any('IsDeleted').x();

// with beetle.queryExtensions.js we can write queries like these;
// this query will be executed immediately and returns true or false
var hasExpired = self.entities.any('IsExpired == true');
// below query will be executed after it's length property is accessed (like LINQ GetEnumerator)
var news = self.entities.where('IsNew');
{% endhighlight %}
* Add a new entity
{% highlight javascript %}
var net = manager.createEntity('EntityType', {Id: beetle.helper.createGuid()});
net.Name = 'Test EntityType';
{% endhighlight %}
* Delete an entity
{% highlight javascript %}
manager.deleteEntity(net);
{% endhighlight %}
* Save all changes
{% highlight javascript %}
manager.saveChanges()
    .then(function () {
        alert('Save succesfull');
    })
{% endhighlight %}

##Supported Data Types
string, guid, date, dateTimeOffset, time, boolean, int, number (for float, decimal, etc..), byte, enum, binary, geometry, geography (spatial types are supported partially, can be fully supported once we decide how to represent them at client side)

##Validators
required, stringLength, maximumLength, minimumLength, range, emailAddress, creditCard, url, phone, postalCode, time, regularExpression, compare

##Supported Query Expressions
ofType, where, orderBy, expand (include), select, skip, top (take), groupBy, distinct, reverse, selectMany, skipWhile, takeWhile, all, any, avg, max, min, sum, count, first, firstOrDefault, single, singleOrDefault, last, lastOrDefault

##Supported Query Functions
toupper, tolower, substring, substringof, length, trim, concat, replace, startswith, endswith, indexof, round, ceiling, floor, second, minute, hour, day, month, year, max, min, sum, count, avg, any, all, contains
(can be used in expression strings, some are not supported by OData but can be used with beetle query string format)

##License
See [License](https://github.com/umutozel/Beetle.js/blob/master/LICENSE)
