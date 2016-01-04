---
layout: post
comments: true
title:  "Cucumber-JVM testing parallel"
date:   2016-01-04 16:17:47 +0530
categories: cucumber jvm testng parallel 
---

Over the past few days I have been trying my luck with the Cucumber-JVM. I have been an active rubyist and was under the strong impression that migrating to the JVM version would be a cake walk, but soon realized the bitter truth that it is not that easy. Another big concern for me after working with testNG and selenium for the last few years, is that I was missing out on the parallel testing feature. Aslak wrote cucumber at least the JVM version to be tightly coupled with junit and little or no scope for any casual parallel/ concurrent processing. He has his reasons, so do we to request this feature. Unfortunately no body has the time to contribute to the cucumber project to make this possible. Another striking issue was to keep the cucumber layer outside of the core test framework. This is also difficult as cucumber does not allow the api hooks to be extended. With all the road blocks I found my self locked out in my room with a piece of paper and pen for hours together. 

Deep in my heart, I knew that it was difficult but not impossible. I started framing a testng based automation framework for cucumber soon enough. This was mainly experimentation with a lot of hits and miss. Soon I arrived at the structure and classified my framework components.

 ![Framework Structure]({{site.url}}/assets/cucumber-jvm/frameworkStructure.png)
 
- util.DataMapper : This class helps me read the not so important data from external csv file. Please bear in mind that I am not trying to data drive my cucumber features. The external data for me are non decision making data. If at all there is an attempt made towards data driving the features or scenarios then we are defeating the purpose of cucumber.
 
 - conditions.Conditions : This class provides me with custom ExpectedConditions modelled around the selenium ExpectedConditions. This will help me in using waits and verifies in a more easier and readable format. Also I will stop having a a lot of individual wait or verify methods in my design.
 
 - cukes.AbstractSteps : This class will be inherited by the step definitions. As per my design I am planning to keep all my verify in this method. This will allow me to do verifications at the step definitions and not at the page methods.
 
 -cukes.AbstractTest : This class will host all the testNG configuration methods. Please note that we cannot have the cucumber.api.hooks defined here (at least as of now).
 
 - support.AbstractPage : This will host all the page methods like click, enterText, waits etc.

 - support.Find : This will host all the element extraction and property extraction methods. On an ideal day this class will never be invoked directly, but indirectly by the conditions for waits and verifies. If at all this class needs to be invoked directly it means there are some verification/ synchronization methods missing or not being utilized.
 
 - support.Locator : This is a wrapper class around the selenium By class. It provides a logical name to the locator strategy/ expression used. The support is given only for xpath or css expression as all the other expressions can be derived from either of them.
 
 - support.Log : This class is mainly used for logging into the cucumber reports. There is however a catch here. If this class needs to be used, then the scenario should be initialized in the AbstractTest class. This is done easily using the cucumber.api.hooks (@Before) from the actual test project.
 
 The framework is placed in github under [core-framework]({%https://github.com/arjunrajeev/Core-Framework%})
 
 The sample project consuming this framework is also present under [cucumber-project]({%https://github.com/arjunrajeev/cucumber-project%})
 
 Now that you have both the repositories, I suggest you go ahead an give it a try using the _mvn clean install_ command. There should be not reason why they will not execute. The default browser configured is chrome, so please make sure you have the chromedriver available. Also please ensure that the selenium-standalone-server is up and running. 
 
 Please note that I am not trying to execute my scenarios in parallel, but my features are intended to be so. Even I do not have the luxury of time to design my scenarios in parallel and more that that I do not feel that it is needed. Also I have testNG classes that for individual feature files. These classes extend a custom hooks which sets the scenario variable in my AbstractTest class discussed earlier (This is very important or else there will only be null pointer exceptions while executing the features). The testNG xml runs all the features in parallel and can be triggered by maven sure fire plugin. Please note that the formatter used here is json and without it reporting is impossible as I am using the cucumber-reporting jar for the purpose. Once the suites are executed then maven exec plugin executes the main method that publishes the report.
 
 Hope that this article will give you a good start automating in cucumber and testNG.