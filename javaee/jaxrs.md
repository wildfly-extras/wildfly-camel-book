## Integration with JAX-RS
JAXR-RS integration with the Camel subsystem leverages the WildFly [RESTEasy](http://resteasy.jboss.org/) module for publishing RESTful services. The [Camel Proxy](http://camel.apache.org/using-camelproxy.html) provides a means of integrating RESTful services into Camel routes.

## JAX-RS service interface
```java
@Path("/greet")
public interface GreetingService {
    @GET
    @Path("/hello/{name}")
    @Produces(MediaType.APPLICATION_JSON)
    public Response sayHello(@PathParam("name") String name);
}
```  
## JAX-RS service implementation class
The Camel context is injected into the JAX-RS service where it is used to lookup an endpoint within a Camel route. The Camel proxy ProxyHelper class creates a proxy service object which is invoked whenever the REST service sayHello method is invoked.

```java  
public class GreetingServiceImpl {

    @Inject
    @ContextName("system-context-1")
    private CamelContext context;

    private GreetingService greetingServiceProxy;

    @PostConstruct
    public void initServiceProxy() throws Exception {
        Endpoint endpoint = context.getEndpoint("direct:start");
        greetingServiceProxy = ProxyHelper.createProxy(endpoint, GreetingService.class);
    }

    public Response sayHello(String name) {
        return greetingServiceProxy.sayHello(name);
    }
}
```

## JAX-RS Application
```java
@ApplicationPath("/rest")
public class RestApplication extends Application {
    @Override
    public Set<Class<?>> getClasses() {
        final Set<Class<?>> classes = new HashSet<>();
        classes.add(GreetingServiceImpl.class);
        return classes;
    }
}
```

## JAX-RS Camel RouteBuilder
The RouteBuilder configures a 'direct:start' endpoint which is will be bound by the Camel proxy that we configured earlier. Any invocations on the GreetingService REST endpoint sayHello method will cause the direct:start route to run.

A custom processor provides the JAX-RS service implementation logic. A BeanInvocation object is passed as the message payload and can be obtained from the 'in' exchange body.

The BeanInvocation object provides access to information about what service method was invoked together with any method arguments. 

```java  
@Startup
@ApplicationScoped
@ContextName("rest-context")
public class RestRouteBuilder extends RouteBuilder {

    @Override
    public void configure() throws Exception {
        from("direct:start")
        .process(new Processor() {
            @Override
            public void process(Exchange exchange) throws Exception {
                BeanInvocation beanInvocation = exchange.getIn().getBody(BeanInvocation.class);
                String name = (String) beanInvocation.getArgs()[0];
                exchange.getOut().setBody(Response.ok().entity("Hello " + name).build());
            }
        });
    }
}
```  
