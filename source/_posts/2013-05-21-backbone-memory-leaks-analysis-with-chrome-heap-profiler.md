---
layout: post
title: "Backbone Memory leaks analysis with Chrome Heap Profiler"
date: 2013-05-21 15:53
comments: true
categories: 
keywords: "backbone, javascript, chrome, heap profiler, memory, leak, web, development, analysis, troubleshooting"
description: "How to use the Chrome Heap Profiler to troubleshoot memory leaks in Backbone applications"
---


Intro
---------

The Heap Profiler in Chrome is a powerful tool to analyze memory usage in web applications. Here are some useful links :

- [Memory Analysis 101](https://developers.google.com/chrome-developer-tools/docs/memory-analysis-101)
- [Heap Profiling](https://developers.google.com/chrome-developer-tools/docs/heap-profiling)


Why do we have to worry about memory management in JS when there is a Garbage Collector ? Because memory leaks can still happen : as long as an object is referenced by another it cannot be disposed by the GC. These references are "Retaining Paths" in the Heap Profiler. 

The majority of memory problems happen because of event binding : events are references which prevents object to be destroyed after they are removed from the DOM. This is a common problem with Backbone Views.


Example
-----------

Here is a simple example (using the Backbone global object as an event mediator) :

```javascript
<html>
<body>
    <script src="jquery.js"></script>
    <script src="underscore.js"></script>
    <script src="backbone.js"></script>
    <script src="zombieViews.js"></script>

    <script>

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

    </script>
</body>
</html>
```

Console Ouptput :

{% img /images/backbonemem/100xzombie.png %}

Nothing should appear in the console because every view is removed after its creation. As we can see this is not the case : the views are still catching `greetingEvent` and displaying a message. 

A heap snapshot confirm that all the ZombieViews are still here (extended Backbone models have their constructor named `child`, see [this post](/blog/2013/05/21/identify-backbone-models-in-chrome-heap-profiler/) for a solution).

{% img /images/backbonemem/takesnapshot.png %}

{% img /images/backbonemem/childpath.png %}



Why the views aren't removed ?



Troobleshoot
----------------

If we take a heap snapshot we can see that the greetingEvent is in the retaining path of the views. This path prevent the GC to dispose the object.

{% img /images/backbonemem/retainingpath.png %}

Let's modify the code with a call to `listenTo` instead of `Backbone.on`. With `listenTo` models can keep track of events binded so they can be unbinded by the remove method and thus allow the GC to dispose the attached views.


```javascript
    //Backbone.on('greetingEvent', this.sayFoo, this);
    this.listenTo(Backbone, 'greetingEvent', this.sayFoo);
```


This time nothing shows up in the console, which is the expected output : all the views where removed, none catched the event.

*Note: You must have noted that the views have their constructor named 'child' which complicate identification, a solution is provided in [this post](/blog/2013/05/21/identify-backbone-models-in-chrome-heap-profiler/).*