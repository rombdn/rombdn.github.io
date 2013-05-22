---
layout: post
title: "Performance analysis : impact of resources saturation on response time"
date: 2013-03-28 10:00
comments: true
categories: 
---

Intro
------
When investigating performance problems, checking saturation is a good place to start : it means there is queueing to access certains resources.

While in batch processing saturation is usually not a problem (except when throughput is affected), this is not the case for user transactions : any saturation will affect response time.

Saturation may appear at different levels, for example :

- System level : CPU (Runqueue), I/O (response time, queue), Memory (swap)... You can find a good list here : [Linux Performance Checklist](http://dtrace.org/blogs/brendan/2012/03/07/the-use-method-linux-performance-checklist/)

- Application level : Application servers messages queue, connections pools...


Utilization vs Saturation
-------------
To illustrate the differences between utilization and saturation I made these graphs of an hypothetical user transaction, on a single core CPU system. Runqueue = 2 is the normal situation (one thread can run on CPU and the other can wait for memory or I/O).


Transaction response time :

![alt text](http://rombdn.files.wordpress.com/2013/03/resptime1.png "Response Time")


CPU Utilization :

![alt text](http://rombdn.files.wordpress.com/2013/03/cpupercent.png "CPU Utilization")


RunQueue :

![alt text](http://rombdn.files.wordpress.com/2013/03/cpurunqueue2.png "Runqueue")


Not very realistic ;), but it shows some empirical observations :

- Transaction response time is not (strongly) correlated with physical resources utilization, contrary to saturation (runqueue).</li>

- The system can generally handle some saturation ( 3 &lt; runqueue &lt; 5) but after a certain threshold response time will rapidly degrade.</li>