## Integration with JAX-WS

WebService support is provided through the [camel-cxf](http://camel.apache.org/cxf.html) component which integrates with the WildFly WebServices subsystem that also uses [Apache CXF](http://cxf.apache.org/).

```java
// Create the CamelContext
CamelContext camelctx = contextFactory.createWildflyCamelContext(getClass().getClassLoader());
camelctx.addRoutes(new RouteBuilder() {
    @Override
    public void configure() throws Exception {
        from("direct:start").
        to("cxf://" + getEndpointAddress("/simple") + "?serviceClass=" + Endpoint.class.getName());
    }
});
camelctx.start();

ProducerTemplate producer = camelctx.createProducerTemplate();
String result = producer.requestBody("direct:start", "Kermit", String.class);
Assert.assertEquals("[Hello Kermit]", result);
```
