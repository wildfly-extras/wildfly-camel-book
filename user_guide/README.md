# User Guide

This chapter takes you through the first steps of getting WildFly Camel and provides the initial pointers to get up and running.

## Download the Distribution

WildFly Camel is distributed as

1. WildFly Patch - [wildfly-camel-patch](https://repository.jboss.org/nexus/content/groups/public-jboss/org/wildfly/camel/wildfly-camel-patch/)
2. Docker Image - [tdiesler/wildfly-camel](https://registry.hub.docker.com/u/tdiesler/wildfly-camel/)


## Installing the Camel Subsystem

Simply apply the patch to the respective wildfly version.



## Integration with JMX

Management support is provided through the [camel-jmx](http://camel.apache.org/jmx.html) component which integrates with the WildFly JMX subsystem.

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

