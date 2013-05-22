---
layout: post
title: "JMeter HTTP tips and examples"
date: 2012-12-17 16:00
comments: true
categories: 
---

Load all resources attached to a html file
--------------

![alt text](/images/jmeter/http-request.png)

Useful when working with frames to avoid one http request per frame (I use this a lot when scripting for PeopleSoft ERP).


Extract a value to a variable with a regular expression
------------------------

![alt text](/images/jmeter/regex1.png)

You can use a Debug Listener to adjust the regexp.
In this example (PeopleSoft) I checked "Main sample and sub-samples" because the value to be extracted is in a frame.


Test a variable
---------------------------

![alt text](/images/jmeter/if-test.png)

In this example I verify that the Regular Expression hereabove worked.


Javascript in parameters (e.g. to randomize a choice)
-----------------------------

*First we get the number of values :*

![alt text](/images/jmeter/random-1.png)

*Then we replace the parameter with our javascript :*

```javascript
Math.floor( Math.random() * ${VALUES_COUNT} ) )
```

*In JMeter it becomes :*

```javascript
${__javaScript( Math.floor( Math.random() * ${NB_Lignes } ) ) }
```

![alt text](/images/jmeter/random-2.png")

*Note : The double `$$` is due to variable names in PeopleSoft (`AF_PCC_WRK_SELECT_PB$1`, `AF_PCC_WRK_SELECT_PB$2`...)*