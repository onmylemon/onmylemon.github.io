---
layout: post
title: Two Quick and Easy Javascript String Methods
description: "The move away from WordPress to a much better(?) system."
tags: [javascript, ninja code, snippets]
image:
  feature: featured/2014-06-25-two-quick-and-easy-javascript-string-methods.png
comments: true
share: true
---

Most programming languages have well defined and widely implemented string methods to assist with your string manipulation. Here are two basic but wonderfully useful methods for working with strings in Javascript.

<!-- more -->

### capitaliseFirst

This method easily capitalises the first letter of a string, nice and easy to use, and to understand.

{% highlight javascript %}
/**
* capitaliseFirst
*
* Makes the first letter of a string a capital
* @param {string}
* @return {string}
*/
capitaliseFirst = function (text) {
    return text.substring(0, 1).toUpperCase() + text.substring(1);
};
{% endhighlight %}

### trimToWord

This will trim a string to the nearest word boundary. If you plug in the string “Have a nice day” with a length of 10 you will get back “Have a”. Useful where you need to trim to the nearest word!

{% highlight javascript %}
/**
* trimToWord
*
* Trim a string to one word less than @length characters
* @param {string} - The string to be trimmed
* @param {int} - The maximum length of the returned string
* @return {string}
*/
trimToWord = function (string, len) {
    if (string.length >= len) {
        string = string.substring(0, len);
        string = string.substring(
            0,
            Math.min(
                string.length,
                string.lastIndexOf(" ")
            )
        );
    }
    return string;
};
{% endhighlight %}

### And thats it

Just a quick post this one, I hope its been useful to someone. Feel free to comment if you have any changes that you think should be made.
