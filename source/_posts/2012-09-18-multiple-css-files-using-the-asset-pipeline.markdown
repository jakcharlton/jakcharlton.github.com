---
layout: post
title: "Multiple CSS Files Using the Asset Pipeline"
date: 2012-09-18 08:34
comments: true
categories: [Ruby, Rails, Tips and Tricks]
---
In Rails, using the Asset Pipeline, you generally use a line in your application template that looks something like:

{% codeblock application.html.erb %}
<%= stylesheet_link_tag "application", :media => "all" %>
{% endcodeblock %}

This tells Rails to load up application.css as a stylesheet, but under the covers, the chances are your stylesheet looks like:

{% codeblock application.css %}
/* ...
*= require_self
*= require_tree .
*/
{% endcodeblock %}

application.css is actually a manifest file, telling Sprockets (what sits under the asset pipeline), to combine a number of different files (css and scss) into a single application.css file in the final application.

So, if you want another stylesheet, as we did for our admin section, you would think that you would limit the scope of your application.css manifest, add a new manifest file for admin, and in your admin template include the admin.css file instead:

{% codeblock application.css %}
/* ...
*= require_self
*= require_tree application_css/.
*/
{% endcodeblock %}

{% codeblock admin.css %}
/* ...
*= require_self
*= require_tree admin_css/.
*/
{% endcodeblock %}

{% codeblock admin.html.erb %}
<%= stylesheet_link_tag "admin", :media => "all" %>
{% endcodeblock %}

And, you would be right, all of this works beautifully ... different stylesheets for the main app and the admin sections, you can now have Twitter Bootstrap to admin your site, and use HTML5Boilerplate or Skeleton CSS to layout your front end.

But with one small problem ... it all works just fine in development and test, but not when you go to production. In production it will complain about a missing stylesheet - and checking the assets folder will show you admin.css isn't there.

The missing step is a tricky one, but as you use assets:precompile in a production environment, you need to specifically tell Rails to compile your admin.css file too. In addition, if you have used the same trick for Javascript, you need to tell Rails about that as well:

{% codeblock config/environments/production.rb %}
# Precompile additional assets
# (application.js, application.css, and all non-JS/CSS are already added)
config.assets.precompile += %w( admin.css admin.js admin.css.scss )
{% endcodeblock %}

It works in development and test as the assets are not precompiled there, and it appears the asset pipeline just looks directly on disk for a manifest file matching the one specified in your template. But it seems the pipeline uses a different rule when it runs assets:precompile and needs you to tell it specifically which files are manifest files.

