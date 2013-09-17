---
layout: post
title: "Paris Public Transports route planning webapp for Firefox OS"
date: 2013-08-21 12:00
comments: true
categories: 
keywords: "firefoxos, firefox os, javascript, dijkstra, graph, metro, bus, public transports, development, webapp"
description: "Paris Public Transports route planning webapp for Firefox OS, using RATP Open Date and shortest path in Graphs"
---

**TL;DR** : RATP Open Data (GTFS) -> JSON graph -> Dijkstra with Priority Queue in JS -> Firefox OS webapp

I released a proof of concept webapp for Firefox OS on the Firefox Market Place : [Metro Bus Paris Route Planning](https://marketplace.firefox.com/app/metro-bus-paris-route-plann-1).

This app is based on the RATP Open Data (Paris Public Transports operator) and features a full client graph shortest path through the whole transport network (subway and bus).

{% img http://www.rombdn.com/fxos-metrobusparis/images/screenshot1.png %}
{% img http://www.rombdn.com/fxos-metrobusparis/images/screenshot2.png %}

**You can try it here** : http://www.rombdn.com/fxos-metrobusparis/demo2/

**Source** : https://github.com/rombdn/fxos-metrobusparis

**Source for the DATA transformation** : https://github.com/rombdn/ratp-gtfs-to-json

*External libs : Leaflet (map), Firefox OS Building Blocks, JQuery and Bootstrap (for autocompletion, didn't have time to implement mine)*


#Background

When the RATP opened their data earlier this year I thought it was a good opportunity to create a better route planning app : the available apps for iOS or Android, official or not, have multiple drawbacks :

 - Slowness : the official app has full Ad splash screen
 - No offline mode
 - Not web apps : fragmentated user experience and unavailability on alternate plateforms like Firefox OS
 
I started hacking with theses goals which put severe constraints :

 - The full subway (metro) and bus graph, including edges with informations like durations, had to fit in less than a MB to minimize loading time
 - The shortest path algorithm had to be fast (less than several seconds)
 

#Conception and implementation

##DATA transformation (GTFS -> JSON Graph)

The original data were way too complex for my goals : 1GB of flat text files with informations on every route, trips, stop times for each day of the year...
Some simplifications I made :

 - Collapse stations : in the original DATA, stations are duplicated for every direction (route) and every line. This would have leaded to an overly complicated graph
 - Average durations and wait times
 
The final graph contains approximatly *6000 stations* (stops) and around *3 edges* per stations instead of *26000 stations* and *up to 20 edges* per station.

**Link to data transformation Github project** : [ratp-gtfs-to-json](https://github.com/rombdn/ratp-gtfs-to-json)


###Original DATA

Full description : http://data.ratp.fr/?eID=ics_od_datastoredownload&file=88

*routes.txt* : route_id, trip_id, type, line (directory)

*trips.txt* : route_id, trip_id 

*stop_times.txt* : stop_id, trip_id, arrival_time  

*stops.txt* : stop_id, stop_name 
   
   
###Graph output :

```javascript
    {
        <stop_id>: {
            name: <name>,
            loc: {
                lat: <latitude>,
                lon: <longitude>
            }
            zip: <zipcode>,
            edges: [
                {
                    "dest": <dest_stop_id>,
                    "dur": <edge_duration>,
                    "type": <edge_type>,
                    "open": <hour_of_opening>,
                    "close": <hour_of_closing>,
                    "line": <line_number>,
                    "dir": <line_direction>,
                    "freq": <average_frequency>
                }
            ]
        }
    }
```

This is achieved with 2 scripts : the first in C to crunch the big (650MB, millions of entry) stop_times.txt to create the raw edges, and the second in ruby to create the graph.


##Javascript Dijkstra with Priority Queue

The funniest part :) It was also the most incertain since I had absolutely no ideas how much time the shortest path search would take, especially on mobiles.
The first version was a straight forward implementation of the algorithm. It was in fact too slow so I decided to use a *priority queue*.

I started from a neat binary heap implementation from the book *Eloquent Javascript* by Marijn Haverbeke [link](http://eloquentjavascript.net/appendix2.html).
To use it in Dijkstra's algorithm I just added an index to each element.

[Binary Heap with Keys source](https://github.com/rombdn/fxos-metrobusparis/blob/master/lib/binaryheap.js) (`idFunction` is the method called to get the index)


##Firefox OS final app

This part should have been trivial but since I'm not an expert in CSS the off canvas layout caused me a lot of headaches ;).
It was in part because I really wanted my app to work on both Chrome, Firefox and the stock android browser (the app still doesn't show properly on the last one).