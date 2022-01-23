---
title: Restricting an ojInputText to accept only numbers
date: 2016-04-22
tags: ['oraclejet', 'javascript', 'html']
author: Buddha
description: The ojInputText component of OracleJet doesn't let you restrict the input. Here is a solution that rejects any non numeric characters.
---
Oracle Jet is a beautiful toolkit for simplifying lot of tasks. ojInputText is a basic editor the framework provides, it can validate the text entered based on the regular expression we give, but validation only happens on blur and if we simply want to filter any keystrokes that don't match that, we can't do it by default.

Here is the result before we go and learn how to do it.
{% codepen mErVGe 'Allow only numbers in OjInputText' result wait %}

Ofcourse we can use ojInputNumber and use the example they gave for eating non-numbers, but what if we don't want the increment and decrement the arrows of ojInputNumber. One way to do it is to bind a keyUp event and check everytime a character is pressed. Infact this is the approach that is used for the example given in OracleJet cookbook.

Here is an alternative approach using ojInputText. Instead of bindng to value, we can bind to rawValue attribute. This ensures that the observable gets updated on every keystroke. <!-- more -->To acheive the result, I have created a helper observable which is a computed observable depending on the value of text box. Whenever a new key is pressed, the computation of helper observable takes place. Here, I used simple javascript regex method to cleanup. Once the cleanup is done, I'm updating the original input text if the value has changed, otherwise I'm leaving it alone. See the below Pen, if you want to see the code and the ojInputText in action.

{% codepen mErVGe 'Allow only numbers in OjInputText' html %}
