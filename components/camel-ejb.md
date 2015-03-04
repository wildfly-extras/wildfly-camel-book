## camel-ejb

The [camel-ejb](http://camel.apache.org/ejb.html) component binds EJBs to Camel message exchanges.

```java
    CamelContext camelctx = new DefaultCamelContext();
    camelctx.addRoutes(new RouteBuilder() {
        @Override
        public void configure() throws Exception {
            from("direct:start").to("ejb:java:module/HelloBean");
        }
    });
```

