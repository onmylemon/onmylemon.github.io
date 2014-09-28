---
layout: post
title: Running away from WordPress
description: "The move away from WordPress to a much better(?) system."
tags: []
image:
  feature: featured/2014-09-28-The-move-to-another-platform.png
comments: true
share: true
---

> _That's all folks, goodbye [WordPress](https://wordpress.org/) and this time it's for good!_

WordPrss has been a good platform for a few years, but with ongoing issues with updates and a number of other concerns I have about it's security, it is time to move on. I've chosen [Jekyll](http://jekyllrb.com/) as my next platform for a few reasons, I'll run through them now.

__Server Side Comparison__

|              | WordPress                   | Jekyll      |
|:------------:|:---------------------------:|:-----------:|
| Hosting      | PHP + Webserver             | Webserver   |
| Speed        | PHP Interpreter + DB select | Static HTML |
| Dependencies | PHP + MySQL + Webserver     | Webserver   |
{: width="100%" }

WordPress has a lot of server side requirements including a relational database, PHP interpreter, and webserver. Jekyll on the other side of things only has the requirement of a webserver and a local computer to build on.
<!-- more -->

__As a Developer<sup>TM</sup>__

Jekyll is a breath of fresh air! Easy to customise, no need to worry about failing updates on a live system, all the things you would expect of a decent system. All editing and building can be done from the command line, which really helps when you spend most of your life there.

Hosting wise, [Github](https://github.com) comes to the rescue with the Github Pages feature, allowing me to host my blog on a platform that has (as of writing) a 99.9977% uptime, and an average response time of 76ms over the [last month](https://status.github.com/graphs/past_month). The other big benefits are treating the blog like a git project, allowing pull requests and all the other git goodness.

__The Security Crunch__

Security issues are a prevalent problem in today's internet (and the internet of the past), unfortunately WordPress just doesn't cut it. With 6 vulnerabilities published over the month of August 2014, from DoS, to CSRF Bypass, and Remote Code Execution, it just isn't good enough [[Reference](http://www.cvedetails.com/vulnerability-list/vendor_id-2337/product_id-4096/Wordpress-Wordpress.html)].

Jekyll, being a static stored site, served only via a webserver with a few links to other apis, lacks many attack surfaces present in WordPress. The majority of vulnerabilities exist in the build process and can be tested for prior to rolling out.

There are of course the vulnerabilities that exist in all web services but using a trusted third party such as Github, I can sleep well at night.

__At the end of the day__

Jekyll is my new blogging platform. WordPress, you have given me a nice couple of years but now I have to go to pastures new. Hopefully Jekyll will treat me nicely and I can have a few glorious years out of it.
