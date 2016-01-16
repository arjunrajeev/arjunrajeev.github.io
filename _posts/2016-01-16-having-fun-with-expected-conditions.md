---
layout: post
comments: true
title:  "Having fun with ExpectedConditions"
date:   2016-01-16 15:51:47 +0530
categories: java selenium expectedconditions
---
To those who are new to selenium's ExpectedConditions, it's an api provided by selenium to support the webdriver waits. These are explicit wait conditions useful in providing a dynamic wait in application areas where a user is not aware how long he has to wait or where in he has to wait for an event to happen. To those who are already using the ExpectedConditions, the question is how exactly they use it. The obvious answer is they use it with webdriver waits. Now what most people are not aware is how it can be easily tweaked to be used with Asserts. In this post I will be talking mainly about using a slightly modified version of the ExpectedCondition so that they can be utilized for both asserts and webdriver waits. Also I will be covering a little about the AssertThat method provided by junit and testng in conjunction with hamcrest.

How many assertions methods are available in your test framework? Or let me make a scenario here so that I can explain the usage with an example. Lets say we have to wait or verify the various conditions around the text retrieved from an element. I can thing about the following conditions
 - text equals
 - text equals ignoring case
 - text equals ignoring the white space
 - text contains
 - text starts with
 - text ends with
 - plus the inversion
 
 So we are probably talking about having 5 conditions, 5 negative conditions.. In effect we have a total of 10 conditions just for verifying the text. Now coming back to my original questions how many asserts and how many waits are there for this 10 conditions in your framework. I have seen my friends writing a total of 20 methods (10 for waits and 10 for asserts) to make this happen, which I call a really bad design (DO note that I am not counting any inverse conditions as I am assuming that you will be using some sort of boolean to achieve this, if not may the good god help you). I will try to teach you a simple trick using which you can drill down to just two methods make all this happen. How.... just go through my Conditions class for example
 
{% highlight java linenos %}

/**
 * Conditions class with host all custom expected conditions that needs to
 * implemented for the waits and verify methods.
 *
 */
public class Conditions {
	/**
     * Expected Condition representing the text of a given element in the aut.
     *
     * @param locator expects the locator object representing the test object in the aut.
     * @param text    expects the Hamcrest expression representing the desired outcome.
     * @return ExpectedCondition<Boolean>
     */
    public static ExpectedCondition<Boolean> textOfElement(final By locator, final Matcher<String> text) {
        return new ExpectedCondition<Boolean>() {
            private By _locator = locator;
            private Matcher<String> _matcher = text;
            private String _message = "";
            
            @Override
            public Boolean apply(WebDriver webDriver) {
                if (_matcher.matches(webDriver.findElement(locator).getText())) {
                    _message = _matcher.toString();
                    return true;
                } else {
                    _message = _matcher.toString() + ", but found otherwise";
                    return false;
                }
            }

            @Override
            public String toString() {
                return "Condition - Text Of Element " + _message;
            }
        };
    }
}    

{% endhighlight %} 

That was simple aint so. Now what is Matcher<String>. It is one of the many matcher library provided by hamcrest. Hamcrest is a generic library that provides a series of matchers to help testers write readable assertions. The library itself is beyond the scope of this post so I will just list out a few examples on the usage. Now lets get back on how to use this in waits and asserts.

{% highlight java linenos %}
       
   /**
    * Verify the success of a given condition.
    *
    * @param condition expects the ExpectedCondition<Boolean>.
    */
   public void verify(ExpectedCondition<Boolean> condition) {
       if (condition.apply(getDriver())) System.out.println("Verify " + condition.toString());
       else {
           System.out.println("Verify " + condition.toString() + "\n");
           throw new RuntimeException(condition.toString());
       }
   }

    /**
     * Wait for the success of a given condition.
     *
     * @param condition expects an ExpectedCondition<Boolean>.
     */
    public void wait(ExpectedCondition<Boolean> condition) {
        try {
            new WebDriverWait(webDriver(), 90).until(condition);
        } catch (Exception e) {
            System.out.println("TimeOut exception on " + condition.toString());
            throw e;
        }
    }
    
{% endhighlight %} 

No let's see the usage,

{% highlight java linenos %}

    verify(textOfElement(By.id("dummyElement"), equalTo("dummy")));
    verify(textOfElement(By.id("dummyElement"), equalToIgnoringCase("duMmy")));
    verify(textOfElement(By.id("dummyElement"), equalToIgnoringWhiteSpace("du mmy")));        
    verify(textOfElement(By.id("dummyElement"), containsString("dummy")));
    verify(textOfElement(By.id("dummyElement"), startsWith("dummy")));
    verify(textOfElement(By.id("dummyElement"), endsWith("dummy")));
    verify(textOfElement(By.id("dummyElement"), is(not(equalTo("dummy")))));
    verify(textOfElement(By.id("dummyElement"), is(not(equalToIgnoringCase("duMmy")))));
    verify(textOfElement(By.id("dummyElement"), is(not(equalToIgnoringWhiteSpace("du mmy")))));
    verify(textOfElement(By.id("dummyElement"), is(not(containsString("dummy")))));
    verify(textOfElement(By.id("dummyElement"), is(not(startsWith("dummy")))));
    verify(textOfElement(By.id("dummyElement"), is(not(endsWith("dummy")))));
    
    wait(textOfElement(By.id("dummyElement"), equalTo("dummy")));
    wait(textOfElement(By.id("dummyElement"), equalToIgnoringCase("duMmy")));
    wait(textOfElement(By.id("dummyElement"), equalToIgnoringWhiteSpace("du mmy")));        
    wait(textOfElement(By.id("dummyElement"), containsString("dummy")));
    wait(textOfElement(By.id("dummyElement"), startsWith("dummy")));
    wait(textOfElement(By.id("dummyElement"), endsWith("dummy")));
    wait(textOfElement(By.id("dummyElement"), is(not(equalTo("dummy")))));
    wait(textOfElement(By.id("dummyElement"), is(not(equalToIgnoringCase("duMmy")))));
    wait(textOfElement(By.id("dummyElement"), is(not(equalToIgnoringWhiteSpace("du mmy")))));
    wait(textOfElement(By.id("dummyElement"), is(not(containsString("dummy")))));
    wait(textOfElement(By.id("dummyElement"), is(not(startsWith("dummy")))));
    wait(textOfElement(By.id("dummyElement"), is(not(endsWith("dummy")))));
    
{% endhighlight %} 

Hope this will get you started with optimization of your test classes and trimming down the asserts and waits in your framework and at the same time provide a little extra readability on what you are trying to achieve with an assertion or wait. Have fun.