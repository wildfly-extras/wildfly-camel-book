## Integration with JMS

Messaging support is provided through the [camel-jms](http://camel.apache.org/jms.html) component which integrates with the WildFly Messaging ([HornetQ](http://www.jboss.org/hornetq)) subsystem.

```java
// Create the CamelContext
CamelContext camelctx = contextFactory.createWildflyCamelContext(getClass().getClassLoader());
camelctx.addRoutes(new RouteBuilder() {
    @Override
    public void configure() throws Exception {
        from("jms:queue:" + QUEUE_NAME + "?connectionFactory=ConnectionFactory").
        transform(body().prepend("Hello ")).to("direct:end");
    }
});
camelctx.start();

// Send a message to the queue
ConnectionFactory cfactory = (ConnectionFactory) initialctx.lookup("java:/ConnectionFactory");
Connection connection = cfactory.createConnection();
sendMessage(connection, QUEUE_JNDI_NAME, "Kermit");

String result = consumeRouteMessage(camelctx);
Assert.assertEquals("Hello Kermit", result);
```
