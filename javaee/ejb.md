## Integration with EJB

Management support is provided through the [camel-ejb](http://camel.apache.org/jmx.html) component which integrates with the EJB3 subsystem.

```java
CamelContext camelctx = contextFactory.createWildflyCamelContext(getClass().getClassLoader());
camelctx.addRoutes(new RouteBuilder() {
    @Override
    public void configure() throws Exception {
        String host = InetAddress.getLocalHost().getHostName();
        from("jmx:platform?format=raw&objectDomain=org.apache.camel&key.context=" + host + "/system-context-1&key.type=routes&key.name=\"route1\"" +
        "&monitorType=counter&observedAttribute=ExchangesTotal&granularityPeriod=500").
        to("direct:end");
    }
});
camelctx.start();

ConsumerTemplate consumer = camelctx.createConsumerTemplate();
MonitorNotification notifcation = consumer.receiveBody("direct:end", MonitorNotification.class);
Assert.assertEquals("ExchangesTotal", notifcation.getObservedAttribute());
```



