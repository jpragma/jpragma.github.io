---
layout: post
title: Static content and webjars in Micronaut applications
date: 2021-02-28 16:39
author: isaaclevin
comments: true
categories: [Uncategorized]
---
Sometimes I need to add a very simple UI to my Micronaut applications. Lightweight javascript libraries such as JQuery or AngularJS are quite convenient for this purpose. 
Traditionally, CDNs are used to reference and download these libraries at runtime, but I prefer to use webjars. 
This way, my app will continue to work even if there is no internet connection or if a certain CDN is blocked e.g. by corporate network policies.

### Step 1
Add dependency to the webjar. In gradle this would look like:
{% highlight gradle %}
runtimeOnly('org.webjars:jquery:3.5.1')
{% endhighlight %}

### Step 2
Add following configuration to application.yml
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

### Step 3
Create static resources (html, js, css) under *src/main/resources/public*

### Step 4
Reference webjar javascript library from html
{% highlight html %}
<head>
    <script src="jquery/3.5.1/jquery.min.js"></script>
</head>
{% endhighlight %}