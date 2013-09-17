---
layout: post
title: "Nightlife webapp built with Backbone, Leaflet, Bootstrap, Node.js and MongoDB using Responsive Design"
date: 2013-09-11 12:00
comments: true
categories: 
keywords: "backbone, node, mongodb, webdev, development, webapp"
description: "Nightlife webapp built with Backbone, Bootstrap, Node, MongoDB and Leaflet using a Responsive Design"
---

The idea for this webapp was to be able to have a "real-time" view of what's had been happening in the bars/clubs/pubs around me.

The landing page is a map with places around you fetched from the database (new places are eventually added through a request to OpenStreetMap).
When you click on a place you can see comments from other people and leave yours.

**[Live Demo](http://www.barotweet.com)** (currently down)

**[Github repo](http://github.com/rombdn/barotweet)**


Mobile

{% img http://www.rombdn.com/barotweet/img/screenshot2.png %}

Desktop

{% img http://www.rombdn.com/barotweet/img/screenshot1.png %}

Technically the app features these concepts/technologies :

 - [Backbone](http://backbonejs.org) with clean architecture
 - Twitter Bootstrap
 - [Node.js](http://nodejs.org)/[Express](http://expressjs.com)
 - [MongoDB](http://www.mongodb.org)
 - [Client/Server code sharing](https://github.com/rombdn/barotweet/blob/master/server/routes/route-base.js) (by reusing Backbone models in Node.js for validation)
 - [Require.js](http://requirejs.org)
 - Responsive Design
 - [Leaflet](http://leafletjs.com) with [OpenStreetMap](http://osm.org)
 - Alerts and messages bus