---
layout: post
comments: true
title:  "Effective Locator Strategy for ExtJs automation"
date:   2016-01-27 05:51:47 +0530
categories: automation selenium extjs automation
---
I have been working my way through with automation of an ExtJs application for the last few weeks. Oh boy, had it not been a really annoying journey?? Well guess not. I had the fortune of addressing this challenge that automation engineers all over the world have been facing and been annoyed with. A few years back in one of the engagements we were scanvenging relational xpath and creating tailored methods for certain elements creating an impossibly nightmarish scripts from a maintenance point of view. So this time I thought of investigating better a better technique and that when my world just opened to ComponentQuery.

Component query is what CSS is for DOM elements. The thought process was rather simple. Irrespective of what UI framework is being used, there has to be certain amount of relations defined so that the framework can connect to the it's related counterparts. So take an example of a text box, there is a label that's connected to it. Wait I can create an xpath that connects an this label with the next available input field. Well, to be honest it ain't that simple mate. There are complex relations defined and simple DOM level locators may not provide a solution. An example would be an Ext checkbox, which to HTML is just a button. Even if you are lucky to find this element, how will you check if whether it's checked or unchecked. Oh! I can create more locators. In that case may the good god help you.

In this post I would be explaining the component query feature of ExtJs which can be utilized by any automation tool with the capability of injecting a javascript. Coming back to the text box and the label let's take a look at your xpath, which probably might looks something like - {% highlight javascript %}//label[contains(text(),'Username')]../..//input{% endhighlight %} vs in Component query it will look like - {% highlight javascript %}textfield[fieldLabel='Username']{% endhighlight %}. Now that's is simple. Now this is a java script that is injected, and the complete expression would be {% highlight javascript %}Ext.ComponentQuery.query("textfield[fieldLabel='Username']"){% endhighlight %} in your browser console. Now important thing to note is that what we have used is not CSS though it looks like (but that is pretty cool, I love CSS). All rules applicable to CSS syntax is applicable here as well with a little bonus like you can traverse from child to parent. Go ahead to Sencha sample implementations and have a try.
 
 Now let's dissect the expression. 
 - textfield : is an xtype (dont know why they call it like that). An xtype is associated with every Ext component. So consider it as the tag in html.
 - [fieldLabel='Username'] : is a filter that can be used to narrow down on your component search. fieldLabel is one of the many attributes made available by ExtJs, and there are a lot more available which can be combined to make effective locators without taking any assumptions on which control that you are trying to act upon in your automation script.
 
 On a finishing note, do go to google and search for _"ExtJs Component Query"_ and go through the documentation available for a more thorough understanding.