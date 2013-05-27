---
layout: post
title: "Identify Backbone Models in Chrome Heap Profiler"
date: 2013-05-21 16:15
comments: true
categories: 
keywords: "backbone, javascript, chrome, heap profiler, memory, leak, web, development"
description: "How to identify Backbone Models and Views in the Chrome Heap Profiler"
---

*Note: this post is based on the idea of Named Constructors from the [Coccyx](https://github.com/onsi/coccyx) library*


In [this post](/blog/2013/05/21/backbone-memory-leaks-analysis-with-chrome-heap-profiler/) I showed an example of a memory leak in Backbone, and how to troobleshoot it using the Chrome Heap Profiler :

```javascript
    var ZombieView = Backbone.View.extend({
        initialize: function() {
            Backbone.on('greetingEvent', this.sayFoo, this);
        },

        sayFoo: function() {
          console.log('foo');
        }
    });

    for(var i = 0; i<1000; ++i) {
        var view = new ZombieView();
        view.remove();
    }

    Backbone.trigger('greetingEvent');
```

{% img /images/backbonemem/child.png %}


But identifying these views is not easy because models extended from Backbone Model/View have their constructor named `child` in the Heap Profiler.


{% img /images/backbonemem/childpath.png %}


A solution to this problem would be the possibility to name the constructors. Here is a simple hack to do that :

In backbone.js, around line 1541 :

```javascript
    // The constructor function for the new subclass is either defined by you
    // (the "constructor" property in your `extend` definition), or defaulted
    // by us to simply call the parent's constructor.
    if (protoProps && _.has(protoProps, 'constructor')) {
      child = protoProps.constructor;
    }
    else {
      child = function(){ return parent.apply(this, arguments); };
    }
```

Simply add a case for an optional constructor name :
```javascript
    if (protoProps && _.has(protoProps, 'constructor')) {
      child = protoProps.constructor;
    } 
    else if (_.has(protoProps, 'constructorName')) {
      eval("child = function " + protoProps.constructorName + "() { return parent.apply(this, arguments); };");
    }
    else {
      child = function(){ return parent.apply(this, arguments); };
    }
```

*Note: due to the use of 'eval' it is not safe in production...*


Now we can add a 'constructorName' in our models/views.


### Example with the leaking view from part 1

Adding a constructor name :

```javascript
		var ZombieView = Backbone.View.extend({
		    constructorName: 'Zombie',
```


Now the Views Constructors are correctly named in the Heap Profiler :

{% img /images/backbonemem/zombie.png %}


Hope this will help!