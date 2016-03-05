## Integration with EJB

Management support is provided through the [camel-ejb](http://camel.apache.org/jmx.html) component which integrates with the EJB3 subsystem.

```java
CamelContext camelctx = new DefaultCamelContext();
    camelctx.addRoutes(new RouteBuilder() {
        @Override
        public void configure() throws Exception {
            from("direct:start").to("ejb:java:module/HelloBean");
        }
    });
```



