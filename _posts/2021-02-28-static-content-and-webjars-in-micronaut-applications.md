---
layout: post
title: Static content and webjars in Micronaut applications
date: 2021-02-28 16:39
author: isaaclevin
comments: true
categories: [Uncategorized]
---
<!-- wp:paragraph -->
<p>Sometimes I need to add a very simple UI to my Micronaut applications. Lightweight javascript libraries such as JQuery or AngularJS are quite convenient for this purpose. Traditionally CDNs are used to reference and download these libraries at runtime, but I prefer to use webjars. This way my app will continue to work even if there is no internet connection or if certain CDN is blocked by e.g. corporate network policies.</p>
<!-- /wp:paragraph -->

<!-- wp:heading -->
<h2>Step 1</h2>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>Add dependency to the webjar. In gradle this would look like:</p>
<!-- /wp:paragraph -->

<!-- wp:code -->
{% highlight gradle %}
runtimeOnly('org.webjars:jquery:3.5.1')
{% endhighlight %}
<!-- /wp:code -->

<!-- wp:heading -->
<h2>Step 2</h2>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>Add following configuration to application.yml</p>
<!-- /wp:paragraph -->

<!-- wp:code -->
{% highlight yml %}
micronaut:
  application:
    name: myapp
  router:
    static-resources:
      default:
        paths:
          - "classpath:public"
          - "classpath:META-INF/resources/webjars"
{% endhighlight %}
<!-- /wp:code -->

<!-- wp:heading -->
<h2>Step 3</h2>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>Create static resources (html, js, css) under <em>src/main/resources/public</em></p>
<!-- /wp:paragraph -->

<!-- wp:heading -->
<h2>Step 4</h2>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>Reference webjar javascript library from html</p>
<!-- /wp:paragraph -->

<!-- wp:code -->
{% highlight html %}
<head>
    <script src="jquery/3.5.1/jquery.min.js"></script>
</head>
{% endhighlight %}
<!-- /wp:code -->
