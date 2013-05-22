---
layout: post
title: "Linerider clone with HTML5 and Box2D JS"
date: 2012-12-17 10:00
comments: true
categories: 
---

Here is a little game I made for a friend while playing around with HTML5 Canvas and [Box2D](http://box2d.org) :

**[Dog Sled](http://www.wajrak.com)**, [source](https://github.com/rombdn/dogsled)

I have planned to do some refactor because the code is very hacky.

Despise that I think that some parts could help as real world examples of Box2D usage in javascript, and how to add graphics in front of box2d... All the game code and logic is in index.html (hem... ;) )


Code highlights
-----------------

### Initialisations

```javascript
//...
dog_initcanvas();
dog_initsound();
dog_events(); //mouse and keyboard
dog_initworld();
dog_setupcollisions();
dog_initdraw();
dog_update();
//...
```


### Collisions

```javascript
//...
function dog_setupcollisions()
{
   var listener = new Box2D.Dynamics.b2ContactListener;
   listener.BeginContact = function(contact) {
      //if the dog touch the line we add a flag
      //...
   listener.PreSolve = function(contact) {
      //if the dog eats a bone there's no collision
      //...
   listener.PostSolve = function(contact, impulse) {
      //if the dog touch his house
      //and sounds...
      //...
```


### Main loop

```javascript
//...
function dog_update()
{
	window.requestAnimationFrame(dog_update);

	world.Step(
		1 / 60,	//frame-rate
		10,		//velocity iterations
		10		//position iterations
	);

	world.ClearForces();
	canvas_draw(); //graphics
}
//...
function canvas_draw()
//...
//we iterate through all the bodies
for(body = world.GetBodyList(); body; body = body.GetNext()) {
   //...
   //we use the User Data field to know what to draw
	switch(body.GetUserData()) {
		case 'line':
//...
```


### More to come...


I used **[Box2DWeb](http://code.google.com/p/box2dweb)**, a javascript port of box2d 2.1. It lacks features like rope joint but I realized it too late... 
More up to date ports i didn't try : 

- [Box2D-HTML5](http://code.google.com/p/box2d-html5)
- [Kripken's Box2DJS](https://github.com/kripken/box2d.js)


Have fun!


*Good tutorials : [Seth Ladd's blog](http://blog.sethladd.com/search/label/box2d)*