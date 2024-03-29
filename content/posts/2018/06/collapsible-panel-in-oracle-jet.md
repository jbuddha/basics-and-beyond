---
title: Collapsible panel using OJet & jQuery
date: 2018-06-11T00:00:00.000Z
tags:
  - oraclejet
  - javascript
  - html
  - knockoutjs
  - jquery
  - css
author: Buddha
description: Here you learn how to develop a panel that can be minimized or maximized and close by clicking on respective icons using OracleJet and JQuery
lastmod: '2022-02-02T06:41:04.996Z'
---
Before we begin, let me show you the final output we are going to get. Click Run Pen button if you want to see the collapsible panel in action

{{< codepen id=PzGZMW tabs="result" height=265 >}}

The panel can be minimized or maximized by clicking on the arrow button. It can also be removed completely by clicking close button. How do we achieve this? I have used KnockoutJS, OracleJET and jQuery to achieve the result. RequireJS is also used but only to get the required libraries from CDN. However, OracleJet is predomantly is used for styling alone. Rest of the bindings can be achieved by regular KnockoutJS and jQuery. Read forward to learn how to get the above result.
<!--more-->
If you are not familiar with RequireJS and OracleJet, some of the things are bit tricky at first, hence basic knowledge on this is expected.
## The View
View mainly consists of Two elements. One is title bar and another is collapsible div. We can use font-awesome to get the icons.
### Title bar
Title bar itself presents several problems. How can we push the icons to the right. We can achieve this very easily by seperating the title bar into 3 parts. The Title text, The icons, and the gap. We can very easily fix the icons and title text at their respective ends by making the gap a flex item that grows where as the other two remain fixed.

Oracle Jet provides Flex Bar for this kind of purposes. We can make a div a flex bar by applying `.oj-flex-bar` class. For the children, we have to use `.oj-flex-bar-start` and `.oj-flex-bar-end` classes for title text and icons div. However, we have to keep an empty div and give it `.oj-flex-item` class so that it grows or shrinks automatically when ever the title bar changes its size. This will ensure that icons stay at the end of title bar. Lines 3, 4 & 5 demonstrate the usage of these classes.

For icons, `fa-chevron-circle-down` and `fa-chevron-circle-up` from font-awesome library are used depending on state of the panel. No need to have two different icons as only one of them is used all the time. So, these classes are conditionally added to html based on collapsed state. Used regualar knockout css binding. Observe lines 4 and 5 in the following snippet. `collapsed` is an observable. When collapsed is true, down icon class is used for the `i` tag and when it is false, the down class will be removed. Same thing happens for the up icon but in reverse. Lines 7 & 8 demonstrate the conditional css binding. All other classes `fa oj-margin-start oj-padding-horizontal hoverable` are constant, hence directly set to `class` attribute of icon tag. `fa-times` works well for close button. Both these icons' click events are bound to functions. Close icon is bound to close funtion and min-max button button's click handler is collapse. We shall discuss these further in the view model section. The entire title bar is placed in a div whose id is header.

```md {title=true}
Min-Max Icon binding
```
```html
<div id="panel" class="oj-margin" style="border: 1px solid">
  <div id="header" class="oj-flex oj-panel oj-flex-bar">
    <div class="oj-flex-start" >This is the title</div>
    <div class="oj-flex-item"></div>
    <div class="oj-flex-end oj-panel-alt1 oj-margin-start">
      <i id="min-max-icon" class="fa oj-margin-start oj-padding-horizontal hoverable"
          data-bind="click: collapse,
                     css: {'fa-chevron-circle-down': collapsed(),
                           'fa-chevron-circle-up':  !collapsed()}"></i>
      <i class="fa fa-close oj-padding-horizontal hoverable" data-bind="click: close"></i>
    </div>
  </div>
  <div id="collapsableContent" style="width: 300px;">
    <h1>{{val}}</h1>
  </div>
</div>
```

The collapsable content is placed in a seperate div `#collapsableContent`. I have used knockout punches to bind `val` observable to the content of h1 tag inside collapsible content. How this is collapsed is discussed in the next section.

## The View Model

View model is pretty simple. It has two observables, one is `val` whose content are bound to h1. This observable is not mandatory. The other observable is `collapsed`. This is quite important. Especially for switching minimise and maximise icons. This is either true of false. Upon calling `collapse` event handler, the value will be flipped.

```md {title=true}
View Model 
```
```js
define(['ojs/ojcore', 'knockout'], function (oj, ko) {
  function panelWithIconsContentViewModel() {
    var self = this;

    self.val = "I'm going to be collapsed, I have some text...";
    self.collapsed = ko.observable(false);

    self.collapse = function collapse(event) {
      $("#collapsableContent").slideToggle();
      self.collapsed(!self.collapsed());
    };

    self.close = function collapse(event) {
      $("#panel").remove();
    };
  }

  return panelWithIconsContentViewModel;
});
```


`self.collapse` is a function that gets triggered upon clicking min or max function. jQuery's slideToggle function is used to slide the `collapsableContent` in and out of view giving an impression of minimize.

The other function is `self.close` which is bound to click handler of close icon. This simply removes the entire panel by using jQuery's remove method. You may want to do something more by showing a dialog asking whether user really wants to close or not, I chose to simply close it.

I didn't include other configuration information that is required for oraclejet and other libraries in the above code snippet. Please refer the codepen's javascript resources given for configuration related code. However, if you are creating a seperate viewModel for the panel alone, no other code is necessary.

{{< codepen id=PzGZMW tabs="html" height=265 >}}

----

If you are interested in non technical reading: [An escape or a possibility?](https://unfurledpages.wordpress.com/2016/06/01/an-escape-or-a-possibilty/)
