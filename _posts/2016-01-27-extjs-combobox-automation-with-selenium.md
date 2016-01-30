---
layout: post
comments: true
title:  "ExtJs Combobox Automation with Selenium"
date:   2016-01-27 05:51:47 +0530
categories: java selenium extjs combobox 
---
If you have automated an ExtJs application you would definitely have come across this annoying feature called the "combobox". This is where the automation engineer feels miserable and helpless. The issue being the fact that these comboboxes are not your normal select fields. So the selenium select methods starts failing. The second problem is that until you click on the dropdown button next to this field the list options are not loaded. And lastly and most important the drop down is an unrelated div lying somewhere at the bottom of the html. And I almost forgot to mention that most of the time the html id's for all these controls are dynamically generated. The result an impossible task leading to fragile implementations.

Let's prioritize how we need to proceed. I will be using the Sencha [kitchen sink] to give a real time example there by making the approach a bit clear (I hope so..).
 - Step 1: Come up with a solid locator strategy for the combobox element.
 - Step 2: Create a relative reference to the dropdown button.
 - Step 3: Identify the bound list that appears.
 - Step 4: Select the desired option from the list.
 
Step 1 was the most toughest part for me at least, after which everything just fell in place. The trick here is to stop looking for patterns in your html for element identification(I know this sounds a bit absurd but do go through the remainder of the document you will start agreeing with me). I started looking at the api and then found something called "component query". This is the mechanism that ExtJs uses for element lookup. And to my advantage or fair surprise the syntax is very similar to css or sizzle but the only difference being they don't search for the element from the DOM perspective.So where does this lead me to, yes the javascript executor. All you need to be aware of to utilize this awesome feature is something called the _xType_. These are actually the pnemonic object types used by ExtJs when an element is defined by a developer. For combobox it's simply "combobox". Assuming that you are already on the Kitchen Sink page (link was provided above), open up the browser console and execute the following javascript 
 {% highlight javascript}Ext.ComponentQuery.query(combobox){% endhighlight %} 
 This will return you a list of controls which are ExtJs comboboxes in the given page.Luckily the sample page provided has only one. Expand any of the list item, and you will see a lot of properties that can be used for indentification. For example the component query can be made more specific like 

  {% highlight javascript}Ext.ComponentQuery.query("combobox[billingFieldName='billingState']")){% endhighlight %} 

![Framework Structure](/assets/cucumber-jvm/frameworkStructure.png)

Ain't this look like a CSS locator?? Ok let's get back to business. Once you have expanded the returned js object, you might have seen something called the triggerEl field. This will give you the reference to the dropdown button. And from the trigger get the dom id, bravo you have the html id for the required element which marks the completion of Step 2. Similarly check out the picker field of the object, and you have the html id of the bound list which is now more related to the combobox field than ever. Now just pick up the bound list item and perform a click on the desired li item. 
 
Now go through the below code to get a better insight into what we have done so far. Note that this approach is pretty much applicable to any testing tool of your choice provided they let you execute a java script in the browser.
 
{% highlight java linenos %}

package com.openbook.tests;

import org.openqa.selenium.By;
import org.openqa.selenium.JavascriptExecutor;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.interactions.Actions;
import org.openqa.selenium.remote.DesiredCapabilities;
import org.openqa.selenium.remote.RemoteWebDriver;

import java.net.MalformedURLException;
import java.net.URL;

/**
 * Created by jarvis on 25-01-2016.
 */
public class ExtComboboxTest {

    public static WebDriver driver;
    public static DesiredCapabilities caps;

    public static final String EXTCOMBOBOX = "Ext.ComponentQuery.query(\"combobox\")[0]";
    public static final String COMBOBOXTRIGGERID = "return " + EXTCOMBOBOX + ".triggerEl.elements[0].id" + ";";
    public static final String OPTIONTOSELECT = "Delaware";
    public static final String DROPDOWNID = "return " + EXTCOMBOBOX + ".picker.id;";

    public static void main(String[] args) throws MalformedURLException, InterruptedException {
        //Initiate WebDriver
        caps = new DesiredCapabilities();
        caps.setBrowserName("chrome");
        driver = new RemoteWebDriver(new URL("http://localhost:4444/wd/hub"), caps);
        driver.get("http://dev.sencha.com/extjs/5.0.0/examples/kitchensink/#form-register");
        //Trigger the ext combobox dropdown button for options to load
        WebElement buttonComboboxTrigger = driver.findElement(By.id((String) ((JavascriptExecutor) driver).executeScript(COMBOBOXTRIGGERID)));
        click(buttonComboboxTrigger);
        //Once the dropdown is selected get the id of the boundlist
        String dropdownId = (String) ((JavascriptExecutor) driver).executeScript(DROPDOWNID);
        //Create a relative xpath to the boundlist item
        String li = "//div[contains(@id,'" + dropdownId + "')]//li[contains(text(),'" + OPTIONTOSELECT + "')]";
        //Select the dropdown item
        click(driver.findElement(By.xpath(li)));
    }

    /**
     * Click the desired WebElement.
     *
     * @param element - expects the WebElement to be selected.
     */
    public static void click(WebElement element) {
        Actions action = new Actions(driver);
        action.click(element).build().perform();
    }
}

    
{% endhighlight %} 

Now, you have some fun automating ExtJS comboboxes while I keep exploring the other Ext components that can have a generalized library like the one we discussed now.

[kitchen sink]: http://dev.sencha.com/extjs/5.0.0/examples/kitchensink/#form-register