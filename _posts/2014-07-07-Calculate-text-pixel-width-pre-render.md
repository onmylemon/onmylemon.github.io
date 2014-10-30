---
layout: post
title: Calculate text pixel width pre-render
description: ""
tags: [javascript, snippets]
image:
  feature: featured/2014-07-07-Calculate-text-pixel-width-pre-render.png
comments: true
share: true
---

Working out widths of a text element before the element has rendered in the DOM takes a little trickery, but is relatively easy.

<!-- more -->

I thought through a few different ways to accomplish this and settled on rendering a span element outside of the viewable page. The throw away element, once rendered, has any required classes applied to it before the width is taken and it is destroyed.

{% highlight javascript %}
/**
 * textWidth
 * Get the pixel width of a string before rendering it in the DOM, requires jQuery global
 *
 * @param {string} - The text string you want the pixel width of
 * @param {string} - Any classes that need to be taken into account when calculating
 *
 * @return {integer} - The width in pixels of the text string
 */
var textWidth = function (string, classes) {
    var width, $elem = $('<span></span>');

    $elem.addClass(classes);
    $elem.attr("style", "position: absolute;top: -999px;left: -999px;");
    $elem.text(string);

    $('body').append($elem);

    width = $elem.width();

    $elem.remove();

    return width;
}
{% endhighlight %}
