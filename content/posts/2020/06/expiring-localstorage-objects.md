---
title: Expiring Local Storage Objects in JavaScript
date: 2020-06-10
tags: ['javascript']
author: Jyothi Prasad Buddha
description: Shows a method of expiring items in browser localStorage
---
All the modern browsers have multiple types of storage mechanisms for using in your web applications. You may have already heard of cookies which are small bits of information you can store and they will be automatically expired.

However, cookies can only store small amounts of information. The other kind of storage is sessionStorage, where you can store big chunks of information. However all the data stored in this will be lost as soon as you close the browser tab.

Local storage provides an intermediary option, it can store large amount of information and it will not be lost after the user closes the tab or browser. The data is persisted across sessions. However, we may want a better solution. We want to store large chunks of data across sessions, but we still want to have an option of invalidating after a certain period of time. Let us see how to go on about solving the problem.
<!-- more -->
<a data-flickr-embed="true" href="https://www.flickr.com/photos/140760885@N04/49959194511/in/dateposted/" title="Browser Storages"><img src="https://live.staticflickr.com/65535/49959194511_65bced3703_z.jpg" width="640" height="549" alt="Browser Storages"></a><script async src="//embedr.flickr.com/assets/client-code.js" charset="utf-8"></script>

## Solution
The solution is to wrap localStorage API with a utility. Your application's logic for accessing should encapsulated in a single class. Using localStorage revolves around storing item with a key and fetching the item previously stored.

The utility we are going to create can provide utility methods to save and load from localStorage. The save method will store the item along with the timestamp, where are load method should first check if the item has already expired before returning it. Here is the that does just that.
{% codeblock lang:javascript %}
const EXPIRY_IN_HOURS = 6;

/**
 * Fetches the previously stored item from the local storage, checks if the item
 * has expired, if so, it will return null, otherwise it will return the item.
 *
 * @param {string} key The key to lookup the item.
 */
export const load = (key) => {
  const jsonValue = localStorage[key];
  const item = jsonValue ? JSON.parse(jsonValue) : null;

  if (hasExpired(item)) {
    delete localStorage[key];
    return null;
  }
  return value.data;
};

/**
 * Saves an item to the local storage along with the current time.
 *
 * @param {string} key The key to store the item.
 * @param {*} data The data to store.
 */
export const save = (key, data) => {
  if (key && data) {
    localStorage[key] = JSON.stringify({ data, timestamp: new Date() });
  } else {
    console.error(`Invalid key or data: Key: ${key}, data: ${data}`);
  }
};

/**
 * Check if the item's age and determines if it has to be invalidated.
 */
const hasExpired = (item) => {
  const dt1 = new Date(item.timestamp);
  const dt2 = new Date();
  diffHours = Math.floor(dt2 - dt1) / (1000 * 60 * 60));

  return !item || EXPIRY_IN_HOURS < diffHours;
};

{% endcodeblock %}

In this snippet, we have one auxiliary method to help with detection of expiration. The method hasExpired will calculate the difference in hours. It uses the current timestamp and the and timestamp stored along with data to calculate the age. If the age of the item is more than the expire hours we set, we return true from this method. The load method invokes the hasExpired method to check if the item is expired and return null after deleting the item from localStorage.
{% admonition warning Watchout %}
* However, keep note of the issues that can arise out of this, if the user happens to travel between timezones the expiry may not work appropriately, the solution is to use UTC timestamps all the time.
* Also, we only remove the items when user tries to access them so, if we store an item and never read it again, we may leave that item forever in localStorage. We can solve that as well but it is for a different post.
{% endadmonition %}

There can be other frameworks that may solve this problem elegantly, but what is more interesting than solving problems ourselves.

---
