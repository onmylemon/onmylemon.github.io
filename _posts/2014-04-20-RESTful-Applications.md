---
layout: post
title: RESTful Applications
description: ""
tags: [concepts]
image:
  feature: featured/
comments: true
share: true
---

Most people are familiar with how to create a RESTful application, or at least a RESTlike application using their preferred language. In most languages there are even frameworks that allow for easily creating a RESTful application <a href="#citation-1">[1]</a>. You can see this post as the personal ramblings of an interested person or just as some personal notes on how to REST.

<!-- more -->

<h3>Think Resources, not URLs</h3>
A resource can be seen as any individual type of data, using the example of a blog you could consider a post, or a group of posts as a resource. Say you want to get a list of blog posts, you might expect this to exist at this resource:

<code>http://host/blog/posts</code>

Or if you wanted to get a particular post you might go for:

<code>http://host/blog/posts/RESTful-Applications</code>
<h3>Know your verbs</h3>
The HTTP protocol makes use of methods to describe the action performed on a resource, these are often referred to as HTTP verbs. Common verbs that are used in most RESTful applications are

<strong><code>GET</code></strong> - Get the content of a resource

<strong><code>POST</code></strong> - Create a new resource

<strong><code>PUT</code></strong> / <strong><code>PATCH</code></strong> - Update an already existing resource

<strong><code>DELETE</code></strong> - Delete a resource that already exists
<ul>
	<li>I want to create a new blog post so I would use the POST method against the http://host/blog/posts resource.</li>
</ul>
<ul>
	<li>If I wanted to view the post I had just created I would use GET against the resource http://host/blog/posts/RESTful-Applications.</li>
</ul>
<ul>
	<li>Then, I decide to update a part of the post, I would use a PATCH or PUT against http://host/blog/posts/RESTful-Applications.</li>
</ul>
<ul>
	<li>Finally I scrap the entire blog post by sending a DELETE to the same resource http://host/blog/posts/RESTful-Applications.</li>
</ul>
If you think about each action and the verb / resource pair that goes with it, you might notice that the first request went to the resource http://host/blog/posts, this is because you are adding a new post to the list of blog posts. Whereas when we go to delete the post we are specifically deleting the resource http://host/blog/posts/RESTful-Application.

There are a lot more verbs available in the HTTP protocol, OPTIONS and HEAD amongst others you can view on the <a title="W3C: HTTP Verbs" href="http://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html" target="_blank">W3C website</a> and a nice little collection on <a title="Anne van Kesteren: HTTP Methods" href="http://annevankesteren.nl/2007/10/http-methods" target="_blank">Anne van Kesteren's site</a>.
<h3>Use curl</h3>
Curl is a great tool for debugging and generally exploring HTTP based APIs, in fact I would go so far as saying that any API produced should be structured in such a way that any developer unfamiliar with the project should be able to explore the API using curl <strong>and nothing else</strong>. There are a few arguments you might find useful when testing an API with curl.
<h4>Decide what to output to the terminal</h4>
If you want both response headers and response body:

<code>curl http://host/blog/posts -i</code>

If you want just the response headers:

<code>curl http://host/blog/posts -I</code>

If you want just the body:

<code>curl http://host/blog/posts</code>
<h4>Send some headers</h4>
You can define request headers using the <code>-H</code> option, some common headers include:

"Content-Type" - The mime-type used in the request body

"Accept" - Content-Type that is acceptable for the response body

If we wanted to specify in our request that we expect the api to pass back a response in JSON format we would run this curl command

<code>curl http://host/blog/posts -H "Accept: application/json"</code>

Most commonly used headers can be found <a title="Wikipedia: Header Fields: Requests" href="http://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Requests" target="_blank">here</a>, though it is worth noting that you can use any HTTP headers in your API, even ones you make up <a href="#citation-3">[3]</a>.
<h4>Use different HTTP methods</h4>
You can send a request using any method you like using curl, here we create a new category for blog posts.

<code>curl http://host/blog/categories -X POST -H "Content-Type: application/json" -d '{"name":"New Category Name"}'</code>
<h3>HTTP Headers</h3>
Headers are used by the HTTP protocol to define anything you want about a request or response. This can be the date, the HTTP response code, or anything you like that could be specific to your application or the infrastructure it is running on.
<h3>Content-Type and Accept Headers</h3>
In some APIs you might see a requested as follows:

<code>curl http://host/blog/posts.json -i</code>

or

<code>curl http://host/blog/posts.xml -i</code>

This just feels like all sorts of wrong, these URLs are specifying two distinctly separate resources and not two different formats for returning the same resource. A RESTful API should respond to any request using the content type specified in the <a title="HTTP Accept Header" href="http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.1" target="_blank">Accept HTTP header</a> of the request. Commonly this would be JSON or XML, specified by application/json and application/xml respectively. <a href="citation-2">[2]</a>

So instead of what we have above, the following should be used:

<code>curl http://host/blog/posts -i -H "Accept: application/json"</code>

or

<code>curl http://host/blog/posts -i -H "Accept: application/xml"</code>
<h3>Acquaint yourself with Mime Types</h3>
Mime types are a description of how a particular piece of content is formatted. Your browser can accept lots of different formats of data but usually it is only useful if the data it receives is in HTML format, which has a mime type of text/html. If you are sending data to an application using jQuery's $.ajax method this would usually be done using JSON, with a mime type of application/json.

There are a stupendous number of mime types and an incomplete list can be found on the Sitepoint website <a title="Sitepoint: Mime Types" href="http://reference.sitepoint.com/html/mime-types-full" target="_blank">here</a>.
<h3>Request Body</h3>
Just like the response from a web server contains a body (the HTML that your browser renders), any request made to a web server can contain a body. When making a request you can put whatever you want in the body, although it is best to use a recognised format, like JSON or XML, and declare the type of data being sent using a "Content-Type" header and a mime type.
<h3>Resource Query Strings</h3>
Many people will be familiar with url query strings, an example might be <code>http://host/resource?query=something</code>, you might think this a grave mistake what with all that I have been going on with, but no. Query strings can be used when you want to limit the information an api call returns.

Say you have a resource that returns blog posts, well if you have more than 10 blog posts this might get a little unwieldy. One solution is to allow your api to return 10 posts at a time with additional pages of results obtained by using the ?page query string.
<h3>Pollers</h3>
When polling, if the information you are obtaining is large (maybe a large dataset or similar), poll with a head request. Your API should be able to give the latest update timestamp for any information being requested, and the requesting application should then be able to make a GET request for that updated information if it needs it.

I will more than likely add to this post over time, however I reckon that these observations should be good for the moment. As always, please feel free to comment.

<a name="citation-1"></a> [1] <a title="Implementing Rest - Frameworks" href="https://code.google.com/p/implementing-rest/wiki/RESTFrameworks" target="_blank">Implementing Rest - Frameworks</a>

<a name="citation-2"></a> [2] <a title="Implementing Rest - FAQs" href="https://code.google.com/p/implementing-rest/wiki/FAQ" target="_blank">Implementing Rest - FAQ</a>

<a name="citation-3"></a> [3] <a title="Node HTTP Module: Message Headers" href="http://nodejs.org/api/http.html#http_message_headers" target="_blank">Node HTTP Module - Message Headers</a><span style="line-height: 1.5em;">
</span>
