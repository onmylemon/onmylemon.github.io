---
layout: post
title: Facebook style counter with Bootstrap and FontAwesome
description: "Easily make the Facebook style counters in a bootstrap theme."
tags: [css, snippets]
image:
  feature: featured/2014-06-12-Facebook-style-counter-with-Bootstrap-and-FontAwesome.png
comments: true
share: true
---


The little counters in the corner of [Facebook](https://www.facebook.com/) icons are really good for letting users of the social networking platform know that there is a message or notification waiting for them. Integrating this into your own application can be done without too much hassle and is well worth it for the usability gains achieved.

<!-- more -->

It is possible to do this in [Twitter Bootstrap](http://getbootstrap.com) combined with [FontAwesome](http://fontawesome.io/) with little modification to menu items already in use. Lets take a look at the markup for getting this to work.

{% highlight html %}
<ul class="nav nav-tabs">
  <a class="btn btn-default">
    <i class="fa fa-database">
      <span class="icon_counter icon_counter_red">5</span>
    </i>
  </a>

  <a class="btn btn-default">
    <i class="fa fa-envelope">
      <span class="icon_counter icon_counter_blue">5</span>
    </i>
  </a>

  <a class="btn btn-default">
    <i class="fa fa-fax">
      <span class="icon_counter icon_counter_green">5</span>
    </i>
  </a>
</ul>
{% endhighlight %}

So we can see that this just provides a basic navigation menu that could be anywhere in an application. The span with a class of .icon_counter contains the counter for that icon. Without styling we end up with the following.

![Example One](/images/posts/2014-06-02-facebook/example-one.png)

This is all well and good but we want it to look good, rather than just appending the counter to the icon, this is where our style comes in.

{% highlight css %}
.icon_counter_red {
  background-color : #f00;
}

.icon_counter_blue {
  background-color : #39f;
}

.icon_counter_green {
  background-color : #3c3;
}

.icon_counter {
  color : #fff;
  font-family : "Arial";
  font-size : 14px;
  font-weight : 600;
  position : absolute;

  padding : 0px 2px;
  margin-top : 6px;
  margin-left : -3px;

  -webkit-border-radius : 3px;
  -moz-border-radius : 3px;
  border-radius : 3px;

  -webkit-box-shadow : inset 0px 0px 2px 0px rgba(50, 50, 50, 0.75);
  -moz-box-shadow : inset 0px 0px 2px 0px rgba(50, 50, 50, 0.75);
  box-shadow : inset 0px 0px 2px 0px rgba(50, 50, 50, 0.75);
}
{% endhighlight %}

This produces a much nicer look and feel...

![Example One](/images/posts/2014-06-02-facebook/example-two.png)

Breaking down the CSS rules we have here and going over them one by one reveals:

* .icon_counter_red / blue / green - This sets the colour of the counter
* .icon_counter
  * color - Sets the appropriate colour for the number
  * font-family - On small sized text it is a good idea to manually set a size in case browser\u2019s default font ruins things
  * font-size - Again, manually setting the font size ensures this works regardless of browser
  * font-weight - Making our counter legible by increasing the weight
  * position - Setting absolute positioning allows us to place the counter correctly
  * padding / margin - The padding and margin are used to position the counter bottom right
  * border-radius / box-shadow - Add a slightly more pleasing visual effect to the counters

Feel free to play around and comment with any improvements you come up with.
