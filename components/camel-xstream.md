## camel-xstream

The [camel-xstream](http://camel.apache.org/xstream.html) component provides the XStream [Data Format](http://camel.apache.org/data-format.html) which uses the [XStream library](http://xstream.codehaus.org/) to marshal and unmarshal Java objects to and from XML.

```java
CamelContext camelctx = new DefaultCamelContext();
camelctx.addRoutes(new RouteBuilder() {
    @Override
    public void configure() throws Exception {
        from("direct:start")
        .marshal().xstream();
    }
});

camelctx.start();
try {
    ProducerTemplate producer = camelctx.createProducerTemplate();
    String customer = producer.requestBody("direct:start", new Customer("John", "Doe"), String.class);
} finally {
    camelctx.stop();
}
```