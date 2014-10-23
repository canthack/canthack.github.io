---
author: omer
comments: true
date: 2011-01-22 16:04:46+00:00
layout: post
slug: hello-again-world
title: Hello (again), World!
wordpress_id: 463
---

In case you haven't noticed we have a new domain! While our previous address was pretty neat it confused a lot of people so hopefully this new, simpler one would make life easier for everyone.

There are a [lot](http://codex.wordpress.org/Changing_The_Site_URL) [of](http://www.mydigitallife.info/2007/10/01/how-to-move-wordpress-blog-to-new-domain-or-location/) [articles](http://yoast.com/move-wordpress-blog-domain-10-steps/) and a variety of methods to do this, here is what I did to move our old blog into its shiny new domain.


## Moving Wordpress to a New Domain


[![truck](http://farm1.static.flickr.com/97/211077423_313fdbee24.jpg)](http://www.flickr.com/photos/footnoteblog/211077423/)


Photo: (cc) Jack Satta / Flickr






	
  * Before you start, **backup your files and database!** Moving files is a relatively safe operation but there will be some SQL trickery going on so a database backup is a must.

	
  * Log in to admin panel and go to Settings -> General

	
  * Change the _WordPress address_ and _Site address_ to point to your new domain. After you save these changes you probably will get a 404 as we haven't moved the files yet so don't panic.

	
  * Move all your files to the new document root.

	
  * Log in to your admin panel at the new address: http://<new_domain>/wp-admin

	
  * Because your URL is pretty much hardcoded everywhere in the Wordpress database you need to do replace those instances with your new one. To do that install the [Search and Replace plugin](http://wordpress.org/extend/plugins/search-and-replace/) and do something along the lines of this:
[![](http://canthack.org/uploads/searchnreplace.png)](http://canthack.org/uploads/searchnreplace.png)

	
  * ???

	
  * PROFIT!


That's pretty much it as far as moving Wordpress goes. To preserve existing links to the old address I've placed the following .htaccess file in the old vhost's docroot. It should permanently redirect (301) all the links to the new domain.

[sourcecode]
RewriteEngine On
RewriteCond %{HTTP_HOST} ^.*canthack\.sdfsdfs\.com$ [NC]
RewriteRule ^(.*)$ http://canthack.org/$1 [R=301,L]
[/sourcecode]

That's all folks, do let us know if you spot anything out of the ordinary.
