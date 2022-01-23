---
title: Open Source Patch Utility For Perforce
date: 2016-03-08
tags: ['javafx','opensource','perforce','java']
description: Introduction to an opensource utility for generating patch files for pending changelists in perforce version controlling system
author: Buddha
---

Perforce is a proprietary version controlling system. It has been around for more than 20 years. However, one glaring limitation always bugs the users. The inability to generate proper diff files.
![Where is my patch?](https://farm2.staticflickr.com/1441/25563016955_8a691e760b_n.jpg)
I can anticipate what is going on in your mind, we can generate a diff file by using the command line interface. `p4 diff` helps us generate a diff file. However if you ever had a misfortune of trying to generate patch files on regular basis. You would understand the pain. It works fine as long as you just modify existing files. However, if you have new files added to the workspace to be checked into to the depot, you quickly run out of luck. One more problem is that you can’t control the files included in the diff file based on change list. It gives you diff file for all the files that are modified. Of course, you can give individual file names, but you have to repeat it either one by one or by using common wildcard. To overcome this, I have come up with a simple JavaFX utility.  Using this utility is as simple as it can get.

First step is to download the [Perforce Patcher jar](https://github.com/jbuddha/perforce-patcher/blob/master/dist/perforce-patcher.jar?raw=true). Just double click the jar file to run it.
{% admonition warning Watchout %}
You Need JRE 8 to run this without any additional configuration. If you are using JRE 7, please keep jfxrt.jar in class path or simply copy it to your jre/lib/ext folder. jfxrt.jar is available inside your {jre or jdk}/lib folder.
{% endadmonition %}

Upon opening the application you will be presented with the login screen. Login with your regular perforce credentials, deselect Remember me, if you don’t want to store the data entered by you locally. By leaving the default value your credentials will be saved in the application cache so that you need not enter it again.
![Login Pane](https://farm2.staticflickr.com/1626/25596793045_91c403dfd9_z.jpg)


 If the login is successful, you will be taken to the next screen otherwise check the Log pane for error. Select the local perforce workspace name and the change list id and click Generate Patch button and save the file at your preferred location through the Save dialog which pops up.
![Patch Generation Pane](https://farm2.staticflickr.com/1501/25229331639_f3324f01b4_z.jpg)
{% admonition info %}
You will be logged out upon closing the application automatically.You can download the utility as a runnable jar from the github repository folder.
{% endadmonition %}

You can find the [project and its source](https://github.com/jbuddha/perforce-patcher) in github. If you like the project, and want to contribute by adding more features, please fork the github repo. It is a maven project so building it is as quick as a wink.
