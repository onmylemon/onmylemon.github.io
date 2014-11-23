---
layout: post
title: Laravel workbench update and dump
description: ""
tags: [concepts]
image:
  feature: featured/
comments: true
share: true
---

Really easy small script of convenience to update composer on all your workbench packages and dump workbench autoloaders.

<!-- more -->

{% highlight bash %}
for path in ./workbench/*/*; do
    [ -d "${path}" ] || continue
    echo "Updating workbench: ${path}"
    composer update -d $path > /dev/null
done

echo "Running Autoload Dump"
php artisan dump-autoload > /dev/null
{% endhighlight %}
