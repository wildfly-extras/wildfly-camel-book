## camel-rest

The [camel-rest](http://camel.apache.org/rest.html) component allows you to define REST endpoints using the [Rest DSL](http://camel.apache.org/rest-dsl.html) and plugin to other Camel components as the REST transport.

> **The WildFly Camel Subsystem only supports camel-servlet for use with the REST DSL. Attempts to configure other components will not work**

```java
    CamelContext camelctx = new DefaultCamelContext();
    camelctx.addRoutes(new RouteBuilder() {
        @Override
        public void configure() throws Exception {
            restConfiguration().component("servlet").contextPath("camel/rest").port(8080);
            rest("/hello").get("/{name}").to("direct:hello");
            from("direct:hello").transform(simple("Hello ${header.name}"));
        }
    });
```
