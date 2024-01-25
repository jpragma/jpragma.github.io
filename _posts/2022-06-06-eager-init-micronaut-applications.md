---
layout: post
title: Eager initialization of Singletons in Micronaut Startup Tests
date: 2022-06-06 17:00
author: isaaclevin
comments: true
categories: [Micronaut]
---
In Micronaut applications, Singleton beans are typically initialized lazily by default. 
This deliberate choice aims to enhance the application startup speed. However, a drawback of this approach is that 
potential wiring errors may only surface when the bean is actually initialized. To mitigate this, we can employ a startup test.

Consider the following example:

{% highlight java %}
@StartupTest
class MyAppStartupTest {
    @Inject
    private EmbeddedApplication application;

    @Test
    void applicationIsRunning() {
        assertThat(application.isRunning()).isTrue();
    }
}
{% endhighlight %}

Notice the StartupTest annotation? Its definition is as follows:

{% highlight java %}
@MicronautTest(contextBuilder = TestEagerInitAppContextBuilder.class)
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface StartupTest {
}
{% endhighlight %}

Of particular note is the custom context builder referenced in the annotation, TestEagerInitAppContextBuilder

{% highlight java %}
public class TestEagerInitAppContextBuilder extends DefaultApplicationContextBuilder {
    public TestEagerInitAppContextBuilder() {
        deduceEnvironment(false);
        eagerInitConfiguration(true);
        eagerInitSingletons(true);
    }
}
{% endhighlight %}

In this custom context builder, we explicitly disable automatic environment resolution while requesting eager 
initialization for both singletons and configuration beans. This strategic adjustment allows us to identify and rectify 
potential wiring errors early in the application lifecycle.






