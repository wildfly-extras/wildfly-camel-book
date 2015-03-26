## camel-velocity

The [camel-velocity](http://camel.apache.org/velocity.html) component allows you to process a message using an Apache Velocity template. 

```java
CamelContext camelctx = new DefaultCamelContext();
camelctx.addRoutes(new RouteBuilder() {
    @Override
    public void configure() throws Exception {
        from("direct:start").to("velocity:" + VELOCITY_TEMPLATE);
    }
});
camelctx.start();
```
