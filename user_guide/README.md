# User Guide

This chapter takes you through the first steps of getting WildFly Camel and provides the initial pointers to get up and running.

## Download the Distribution

WildFly Camel is distributed as

1. WildFly Patch - [wildfly-camel-patch](https://repository.jboss.org/nexus/content/groups/public-jboss/org/wildfly/camel/wildfly-camel-patch/)
2. Docker Image - [tdiesler/wildfly-camel](https://registry.hub.docker.com/u/tdiesler/wildfly-camel/)


## Installing the Camel Subsystem

Simply apply the patch to the respective wildfly version.



## Camel Feature Provisioning

WildFly Camel provides feature provisioning similar to Karaf features. A feature is defined as set of abstract Resources with associated Capabilities/Requirements. All known features are stored in a Repository. At runtime the Provisioner gets a set of Resource candidates from the Repository and uses the Resolver to find a consistent wiring solution for the current state of the Environment. After this no-impact analysis, the Provisioner installs the required set of Resources to the Environment if a consistent wiring solution can be found by the Resolver.

The initial set of supported features is part of the WildFly Camel repository content definition. Resources that are already part of the WildFly environment are defined as part of the environment content

A good starting point to work with WildFly Camel feature provisioning is ProvisionerSupport and references to it.

The concepts of Resource, Capability, Requirement, Resolver, Repository, Provisioner are all provided by the Gravia project, which is a rewrite of the same functionality that used to be available in WildFly as part of the JBoss OSGi integration.

WildFly Camel feature provisioning has no dependency on OSGi.

```java
ProvisionerSupport provisionerSupport = new ProvisionerSupport(provisioner);
provisionerSupport.installCapabilities(IdentityNamespace.IDENTITY_NAMESPACE, "camel.cxf.feature");
...
```

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

## Integration with JNDI

The [WildFlyCamelContext](https://github.com/tdiesler/wildfly-camel/blob/master/subsystem/src/main/java/org/wildfly/camel/WildflyCamelContext.java) provides integration with the WildFly Naming subsystem.

```java
WildflyCamelContext camelctx = contextFactory.createWildflyCamelContext(getClass().getClassLoader());

// Bind a bean to JNDI
Context context = camelctx.getNamingContext();
context.bind("helloBean", new HelloBean());
camelctx.addRoutes(new RouteBuilder() {
    @Override
    public void configure() throws Exception {
        from("direct:start").beanRef("helloBean");
    }
});
camelctx.start();

ProducerTemplate producer = camelctx.createProducerTemplate();
String result = producer.requestBody("direct:start", "Kermit", String.class);
Assert.assertEquals("Hello Kermit", result);

context.unbind("helloBean");
```

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

## Arquillian Test Support

The WildFly Camel test suite uses the WildFly [Arquillian](http://arquillian.org/) managed container. This can connect to an already running WildFly instance or alternatively start up a standalone server instance when needed.

A number of test enrichers have been implemented that allow you have these WildFly Camel specific types injected into your Arquillian test cases.

```java
@ArquillianResource
CamelContextFactory contextFactory;

@ArquillianResource
CamelContextRegistry contextRegistry;
```
