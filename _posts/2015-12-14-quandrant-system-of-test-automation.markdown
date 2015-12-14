---
layout: post
comments: true
title:  "The Autamation Quadrant"
date:   2015-12-14 15:51:47 +0530
categories: automation testing
---

I been a while since I provided any kind of training, but the intent is smiple. I have had a lot of junior folks who want to learn autoamtion but not sure how. These folks attend trainings and try self learning but end up frustrated and slowly leave behind their interest in test automation. _It's not every body's bucket_. I would have agreed with it 5 years ago, but off late I am seeing a change in the pattern. Automation is no longer a desired skill. Agile teams make it a mandate that the testers can automate, and if they can't then..... but that is another story for another time.

The next big question is which tool and which language. Now I can't answer it, as this is completely context oriented. I am not aware of a single tool that fits in every requirement that you have (_I would not mind any arguments here and rather welcome it_). So my approach was to define a standard method of learning test automation without introducing any tools. So how exactly are we gonna do this.

Now before going to the solution lets get familiar with some fundamentals

- Automation framework : is a set of assumptions and considerations you take that drives the automation in your project. It can be the basic folder structure to complex data handling. There cab be different types of frameworks that you may come accross and hear many fancy names, but for now they are only assumptions and guidelines.

- Test objects : Some tools call this object repository/ maps / locators. These are important part of your test automation projects. Any automation tool if paid, will have a utility to spy any control(button, radio etc), that displays all the attributes used to define the test object. By defenition we just create a variable/ method/ or external file component that ca be used by your script to perform and action or assertions.

- Test Data : is an external file(s) that is used to place all your test inputs. They can be of xls(x), csv, xml, yml or anything from where you can meaningfully read them.

- Asserts : Assert simply means you are verifying any of your test conditions, period.

I think that is enough to get you started with the learning system. Now let us take a scenario, where a user needs to login to his outlook.com mail box. 
1. User provides his username in the username text field.
2. User provides his password in the password text filed.
3. User clicks on login button.
4. Verify if the login is successful.

Now lets move it to a qudrant. All the actions you perform like clicks, input text, select a radio button etc into the 1st quadrant. All tools will have the extact operations defined in their library no matter what technology they support also keep in your mind that no matter what technology there are only very few types of test obects that an automation engineer needs to handle(_try counting it and you will know what I am talking about_). 

The next part is extracting the contents out of your test objects. Unless you do this you won't be able to perform any kind of validations. So lets move any sort of extrations into the 2nd quadrant. Now this can vary from simple to complex, but the pattern is essentially the same irrespective of the tools used.

Once you have all the properties extracted the next step is validating them. There are tool specific validator or framework based validators. Pick any based on your needs and move it to the 3rd quadrant.

These 3 quadrants are very similar no matter what tools or technology you opt for. And learning these commands would not take more that a week (_seriously  week?_). 3 out of 4 quadrant is equal to 75% of test automation, then what makes it difficult. It obviusly quadrant number 4. The quadrant 4 deals will all sorts of helpers. It can be any how you drive your data/ test. How you read from or modify a database table. How you design various parts of your framework. How does your framework deal with different infrastructural challenges. This quadarant varies drastically based on the technology you use and obviously how good your programming skills are. But that is only 25%. If you breakdownn the activities in the 4th quadrant they are not very hard to be done. There is plenty of materials available on the internet that can help you understand and do things slowly making you proficient in this quadrant. And this is the only quadrant where you need any mentor. 

Hoping that this would help you make a good start in test automation. Please provide any comments you feel appropriate.
