FireBind v0.5.1
========

Knockout bindings for Firebase

There are two objects added to Knockout by this script. ```ko.fireSet``` for observableArray's that are back by Firebase collections, and ```ko.fireModel``` for objects with observable properties that are back by a Firebase location. They are intended to be used together, but either can be used on their own.

Sets
---

Firesets wrap ```ko.observableArray```. ```ko.fireSet``` will return an observableArray that has all of its collection modifying methods replaced with methods that instead call Firebase's methods. It also wires up Firebase events for the supplied location so that they modify the underlying observableArray.

The result is that you have an array that you can bind to, and modify, as your normally would. Like observableArrays, Firesets only track the items in its array, not the properties of those items.

To construct a Fireset, call ```ko.fireSet``` with the Firebase reference for the collection, and a Constructor function to use for the items that are added. This Constructor is passed the id of the object (Firebase calls it a 'name'), the object itself, and the Firebase reference for that object (which is a child of the Set).

A 3rd parameter to ```ko.fireSet```, the ```config```, controls the 'id' property of its children, and the sorting property of its children.

Children *must* have id properties, but the config property will default to 'id' if not supplied. Without an 'id' property, there would be no way to track local adds or remote removal.

Firebase ordered sets are used for all Firesets. By default, the priority will just be a number generated by Firebind. This will allow you to reorder children with the standard array methods as you like, without needing to specify a config property. If you would like the ordering to be done based on a property of the children, you must specify this property with ```config.orderBy```. If you do this, ```reverse``` and ```splice``` (when used to add items) will throw errors if called, since you cannot manually control the order. ```sort()``` will simply do nothing in this case.

To add queries (like limits) to a Fireset, simply add them to the Firebase reference you pass to ```ko.fireSet```.


Models
---
Models are objects that have Computed Observable properties that intercept writes and pass them to Firebase. The underlying observables are wired to Firebase ```value``` events, so that they are kept up to date. The result is an object whose properties are synced with the properties of a Firebase reference.

The intended use pattern is inside of a constructor, like the one called by Firesets. ```ko.fireModel``` does not return an object, instead it modifies the first parameter passed to it (which will be ```this``` inside of a constructor). The second parameter is a map, an object whose properties will be added to the model (the first paramter), and whose values represent defaults. The third parameter is the Firebase reference.

Here is a common example:

	var User = function(id, data, fireRef) {
	    this.id = id;
		var map = {
			firstName: data.firstName || '',
			lastName: data.lastName || ''
		}
	    ko.fireModel(this, map, fireRef);
	};


This constructor can be used by a Fireset.

You can see both of these in use in this fiddle: http://jsfiddle.net/tyrsius/eaeY5/

For quick, easy use, add firebind with this URL: https://rawgithub.com/tyrsius/FireBind/master/firebind.js

Comments, suggestions, and optimizations welcome
