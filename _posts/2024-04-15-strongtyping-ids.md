---
layout: post
title: Beyond Strings - Leveraging Strong Typing for IDs in Java Apps
date: 2024-04-15 19:15
author: isaaclevin
comments: true
categories: [Java]
mermaid: false
---

In a typical Java application of significant size, numerous domain objects are present, 
each often featuring identifiers. These identifiers are commonly defined as Strings, Integers, 
or similar data types within many codebases. Moreover, there might be additional alternative 
identifiers like account numbers or social security numbers. The challenge arises when interface 
methods are exposed, requiring these identifiers as parameters. Without strong typing, 
it becomes ambiguous which identifier should be passed in a given method call. 
Hence, it is crucial to strong-type these identifiers. Additionally, when accepting and providing 
JSON representations of domain objects, it's vital to serialize these identifiers in their raw form.

Here's a possible solution:

{% highlight java %}
public abstract class StrongType<T> {
    private final T value;

    protected StrongType(T value) {
        Objects.requireNonNull(value);
        this.value = value;
    }

    @JsonValue
    public T getValue() {
        return value;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        StrongType<?> that = (StrongType<?>) o;
        return Objects.equals(value, that.value);
    }

    @Override
    public int hashCode() {
        return Objects.hashCode(value);
    }

    @Override
    public String toString() {
        return value.toString();
    }
}
{% endhighlight %}

{% highlight java %}
public abstract class StrongStringType extends StrongType<String> {
    protected StrongStringType(String value) {
        super(value);
    }
}
{% endhighlight %}

{% highlight java %}
public class SSN extends StrongStringType {
    protected SSN(String value) {
        super(value);
    }
}

public class Customer {
    private String name;
    private SSN ssn;
}
{% endhighlight %}

In order to accept strong types as path variables in Spring based REST controllers you need 
to implement and register a ConverterFactory:

{% highlight java %}
@Component
public class StringToStrongTypeConverterFactory implements ConverterFactory<String, StrongType> {

    private static class StringToStrongTypeConverter<T extends StrongType> implements Converter<String, T> {
        private final Class<T> type;

        public StringToStrongTypeConverter(Class<T> type) {
            this.type = type;
        }

        @SneakyThrows
        public T convert(String source) {
            Constructor<?> cons = type.getConstructor(String.class);
            return (T) cons.newInstance(source);
        }
    }

    @Override
    public <T extends StrongType> Converter<String, T> getConverter(Class<T> targetType) {
        return new StringToStrongTypeConverter(targetType);
    }
}

@Configuration
class WebConfig implements WebMvcConfigurer {
    // ...
    @Override
    public void addFormatters(FormatterRegistry registry) {
        registry.addConverterFactory(new StringToStrongTypeConverterFactory());
    }
}
{% endhighlight %}
