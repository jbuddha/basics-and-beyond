---
title: Hello World for Oracle Commerce (ATG)
date: 2016-03-06T00:00:00.000Z
tags:
    - atg
    - java
    - oraclecommerce
author: Buddha
description: This article helps you learn how to begin with creating new components in Oracle Commerce.
thumbnail: 'https://i.stack.imgur.com/sWi9t.png'
lastmod: '2022-01-24T21:25:58.697Z'
---
Oracle Commerce or ATG is an Ocean. There are so many concepts in Oracle Commerce (previously known as ATG), that makes coming up with Hello World program little difficult. 
* Do you mean to create one JSP page and deploy it like commerce reference store? 
* Do you want to create a component just to see in Dyn/Admin? 
* Do you want to create a new repository? 
Depending on what you want to do, the approach to take will be different.

{{< figure src="https://farm2.staticflickr.com/1633/25195316629_65c8162a37_n.jpg" caption="#### Too many options to begin with" >}}

To work with Oracle Commerce, you don’t have to know about persisting data in database. If you approach Oracle Commerce programming with J2EE & MVC experience, you may find it little difficult to cope with it unless you start with a fresh mind, because things are very different in this platform. Today, I will demonstrate how to create a simple component so that it can be viewed in Dyn/Admin. Let us assume that you are trying to create it in your own module instead of existing module like DAS or DAF. Follow the the steps shown below.

## Step 1: Create an Eclipse project

Create an Eclipse project and make sure you add all the necessary class files to the build path. Add classes.jar or DAS at the least.
<!--more-->
{{< figure src="https://i.stack.imgur.com/OP8b7.png" caption="#### Sample Eclipse Project Structure" >}}

## Step 2: Create the Java Class

A component in Oracle Commerce is combination of two files. A Java Class and a Properties File. The Java class can be a simple bean or can be a service that performs several tasks based on a schedule. Simplest way to create the necessary Java class is to extend GenericService.

```md {title=true}
HelloWorldComponent.java
```
```java
package com.buddha.components;

import atg.nucleus.GenericService;
import atg.nucleus.ServiceException;

public class HelloWorldComponent extends GenericService {

    public String firstStr = "Dummy Value";

    public String getFirstStr() {
        return firstStr;
    }

    public void setFirstStr(String firstStr) {
        this.firstStr = firstStr;
    }

    @Override
    public void doStartService() throws ServiceException {
        super.doStartService();
        System.out.println("Hello ATG Component!");
    }

    @Override
    public void doStopService() throws ServiceException {
        super.doStopService();
        System.out.println("Hello ATG Component! Stops now!");
    }
}
```

## Step 3: Create the properties file

The Properties file must be providing the values to the properties in the component. This initialises the bean. `$class` property will link the property file with the class file we have created in previous step. Location of the propety file decides the path of the component instead of the java class. Follow the example below to create the property file.

```md {title=true}
HelloWorldComponent.properties
```
```java
$class=com.buddha.components.HelloWorldComponent
firstStr=HelloWorld
```

Multiple components can be created from the same class file. A different properties file can have same `$class` but initialise firstStr to a different value. This creates a different component.

## Step 4: Create a Manifest file

Manifest files is like a configuration for the module. What all are its dependencies when it is running in webserver, where are the compiled classes placed etc.,

```md {title=true}
Manifest.MF
```
```yaml  
Manifest-Version: 1.0
ATG-Required: DafEar.Admin
ATG-Config-Path: config/
ATG-Class-Path: ./bin/
```

## Step 5: Build & Deploy
Build the project and copy the project folder into ${DYNAMO_ROOT} and run the following command to generate an ear file of your project and deploy it in your jboss server. No need to generate any ear file if you are running it on Tomcat. Just start the respective server with the given module.

```sh {linenos=false}
runAssembler.bat -jboss HelloWorld.ear -m EXP_HelloATGComponentWorld
```
## Step 6: Access the Component

Navigate to Dyn/Admin and search for the component HelloWorldComponent and click on the component listed in the search results.

{{< figure src="https://i.stack.imgur.com/urvDL.png" caption="#### Search Results in DynAdmin" >}}

Click on it to go to the component page to see the property we have created and its value given in properties file.

{{< figure src="https://i.stack.imgur.com/sWi9t.png" caption="#### Component & the property we have created earlier" >}}

You can see the server log to find a line similar to this.

```sh {linenos=false}
INFO  [stdout] /dyn/admin/nucleus//com/buddha/components/HelloWorldComponent Hello ATG Component!
.....
INFO  [stdout] /dyn/admin/nucleus//com/buddha/components/HelloWorldComponent Hello ATG Component! Stops now!
```
This line is generated because of the sysout in our doStartService(); You can also give other methods that can be called through dyn/admin or interact with other components. However in production, avoid using System.out.println, instead use loggingDebug or loggingInfo. Best of Luck.

----

Here is an interesting non-technical blog post that I came across recently: [Reading a book vs Watching a Movie Adaption](https://unfurledpages.wordpress.com/2016/03/21/turning-pages-or-tuning-channels/)
