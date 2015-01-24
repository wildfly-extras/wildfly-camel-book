# camel-mvel

The [camel-mvel](http://camel.apache.org/mvel-component.html) component allows you to process a message using an [MVEL](http://mvel.codehaus.org/) template.

A simple template 

```
Hello @{request.body}
```

can be used like this

```java
CamelContext camelctx = new DefaultCamelContext();
camelctx.addRoutes(new RouteBuilder() {
    @Override
    public void configure() throws Exception {
        from("direct:start").to("mvel:template.mvel");
    }
});
camelctx.start();
ProducerTemplate producer = camelctx.createProducerTemplate();
String result = producer.requestBody("direct:start", "Kermit", String.class);
Assert.assertEquals("Hello Kermit", result);
```