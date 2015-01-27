# camel-activemq

Camel ActiveMQ integration is provided by the [camel-activemq](http://camel.apache.org/activemq.html) component.

The component can be configured to work with an embedded or external broker. For Wildfly / EAP container managed connection pools and XA-Transaction support, the [ActiveMQ Resource Adapter](http://activemq.apache.org/resource-adapter.html) can be configured into the container configuration file.

## Simple Camel CDI Activemq RouteBuilder
```java
@Startup
@ApplicationScoped
@ContextName("amq-cdi-context")
public class ActiveMQRouteBuilder extends RouteBuilder {

    private static String BROKER_URL = "vm://localhost?broker.persistent=false&broker.useJmx=false" +
            "&broker.useShutdownHook=false";

    @Override
    public void configure() throws Exception {

        ActiveMQComponent activeMQComponent = new ActiveMQComponent();
        activeMQComponent.setBrokerURL(BROKER_URL);
        getContext().addComponent("activemq", activeMQComponent);

        from("activemq:queue:testQueue")
            .to("mock:results");
    }
}
```

