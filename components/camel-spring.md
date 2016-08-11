## camel-spring

Camel contexts can be configured with Spring via the [camel-spring](http://camel.apache.org/spring.html) component.

### Camel Spring XML example
```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
       http://camel.apache.org/schema/spring http://camel.apache.org/schema/spring/camel-spring.xsd">

    <camelContext id="hello-camel-context" xmlns="http://camel.apache.org/schema/spring">
        <route>
            <from uri="timer:hello?period=5000" />
            <log message="Hello world" />
        </route>
    </camelContext>

</beans>
```

### Deployment

Spring Camel contexts can be deployed either as part of the camel subsystem configuration or by deploying a '-camel-context.xml' suffixed file.

For more information see the [features](../features/README.md) section.
