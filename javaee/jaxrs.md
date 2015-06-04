## Integration with JAX-RS
JAX-RS consumer support is provided by two mechanisms. The [Camel REST DSL](../components/camel-rest.md) and by leveraging the WildFly [RESTEasy](http://resteasy.jboss.org/) JAX-RS subsystem to publish RESTful services in conjunction with the [CamelProxy](http://camel.apache.org/using-camelproxy.html).

JAX-RS producer endpoint support is provided by the [camel-restlet](http://camel.apache.org/restlet.html) component.

> **IMPORTANT: At present the WildFly Camel Subsytem does not support JAX-RS CXF or Restlet consumers. E.g endpoints defined as from("cxfrs://...") or from("restlet://"). Other JAX-RS consumer endpoint solutions are described later in this document.**

### JAX-RS Restlet Producer

The following code example uses the camel-restlet component to consume a JAX-RS service which has been deployed by the [WildFly JAX-RS subsystem](https://docs.jboss.org/author/display/WFLY8/Java+API+for+RESTful+Web+Services+&#40;JAX-RS&#41;).

#### JAX-RS service
This RESTful service desribes a simple interface which will return a list of Customer POJOs as a JSON string. The service will be published to the path `GET /rest/customer`.

```java
// Service interface
@Path("/customer")
public interface CustomerService {
  @GET
  @Produces(MediaType.APPLICATION_JSON)
  Response getCustomers();
}

// Service implementation
public class CustomerServiceImpl implements CustomerService {
  @Override
  public Response getCustomers() {
    List<Customer> customers = customerService.getCustomers();
    return Response.ok(customers).build();
  }
}

// Application bootstrap class
@ApplicationPath("/rest")
public class RestApplication extends Application {
  @Override
  public Set<Class<?>> getClasses() {
    final Set<Class<?>> classes = new HashSet<>();
    classes.add(CustomerServiceImpl.class);
    return classes;
  }
}
```

#### Camel route configuration
The Restlet component can be used to consume RESTful services as shown in the following Camel RouteBuilder example. CDI in conjunction with the camel-cdi component is used to bootstrap the RouteBuilder and CamelContext.

```java
@Startup
@ApplicationScoped
@ContextName("rest-camel-context")
public class RestProducerRouteBuilder extends RouteBuilder {
  @Override
  public void configure() throws Exception {
    from("direct:start")
    .to("restlet://http://localhost:8080/rest/customer")
    .process(new Processor() {
      @Override
      public void process(Exchange exchange) throws Exception {
        // Do something useful with the REST service response
      }
    });
  }
}
```

### JAX-RS Consumer with the Camel REST DSL

The Camel REST DSL gives the capability to write Camel routes that act as JAX-RS consumers. The following RouteBuilder class demonstrates this.

```java
@Startup
@ApplicationScoped
@ContextName("rest-camel-context")
public class RestConsumerRouteBuilder extends RouteBuilder {
  @Override
  public void configure() throws Exception {
    // Use the camel-servlet component to provide REST integration
    restConfiguration().component("servlet")
      .contextPath("/rest").port(8080).bindingMode(RestBindingMode.json);

    rest("/customer")
      // GET /rest/customer
      .get()
        .produces(MediaType.APPLICATION_JSON)
        .to("direct:getCustomers")
        // GET /rest/customer/1  
      .get("/{id}")
        .produces(MediaType.APPLICATION_JSON)
        .to("direct:getCustomer")
      // POST /rest/customer
      .post()
        .type(Customer.class)
        .to("direct:createCustomer");
      // PUT /rest/customer
      .put()  
        .type(Customer.class)
        .to("direct:updateCustomer");
      // DELETE /rest/customer/1  
      .delete("/{id}")
        .to("direct:deleteCustomer");  
  }
}
```

Note that the REST DSL configuration starts with `restConfiguration().component("servlet")`. **The WildFly Camel Subsystem only supports camel-servlet for use with the REST DSL. Attempts to configure other components will not work**.

By setting the binding mode, Camel can marshal and unmarshal JSON data either by specifying a 'produces()' or 'type()' configuration step.



### JAX-RS Consumer with CamelProxy

JAX-RS consumer endpoints can be configured using the [CamelProxy](http://camel.apache.org/using-camelproxy.html). The proxy enables
you to proxy a producer sending to an Endpoint by a regular interface. When clients invoke methods on this interface, the proxy performs a request / reply to a specified endpoint.

Below is an example JAX-RS service interface and implementation.

```java
// Service interface
@Path("/customer")
public interface CustomerService {
  @GET
  @Produces(MediaType.APPLICATION_JSON)
  Response getCustomers();

  @PUT
  Response updateCustomer();
}

// Service implementation
public class CustomerServiceImpl implements CustomerService {
  @Inject
  @ContextName("rest-camel-context")
  private CamelContext context;

  @Produce(uri="direct:rest")
  private CustomerService customerServiceProxy;  

  @Override
  public Response getCustomers() {
    return customerServiceProxy.getCustomers();
  }

  @Override
  public Response updateCustomer(Customer customer) {
    return customerServiceProxy.updateCustomer(customer);
  }
}
```

Notice in the above code example that `CustomerServiceImpl` delegates all method calls to a customerServiceProxy object which has been annotated
with `@Produce`. This annotation is important as it configures a proxy for the `direct:rest` endpoint against the `CustomerService` interface. Whenever any of the REST service methods are invoked by clients, the `direct:rest` camel route is triggered.

The RouteBuilder class implements logic for each REST service method invocation.

```java
from("direct:rest")
  .process(new Processor() {
      @Override
      public void process(Exchange exchange) throws Exception {
        BeanInvocation beanInvocation = exchange.getIn().getBody(BeanInvocation.class);
        String methodName = beanInvocation.getMethod().getName();

        if (methodName.equals("getCustomers")) {
          List<Customer> customers = customerService.findAllCustomers();
          exchange.getOut().setBody(Response.ok(customers).build());
        } else if(methodName.equals("updateCustomer")) {
          Customer updatedCustomer = (Customer) beanInvocation.getArgs()[0];
          customerService.updateCustomer(updatedCustomer);
          exchange.getOut().setBody(Response.ok().build());
        }
      }
  });
```
In the above RouteBuilder a `Processor` handles REST service method invocations that have been proxied through the `direct:rest` endpoint.
The exchange message body will be an instance of `BeanInvocation`. This can be used to determine which web service method was invoked and
what arguments were passed to it. In this example some simple logic is used to return results to the client based on the name of the method that
was called.

### Security

Refer to the [JAX-RS security section](../security/jaxrs.md).

### Code examples on GitHub

An example [camel-rest application](https://github.com/wildfly-extras/wildfly-camel/tree/3.0.0/examples/camel-rest) is available on GitHub.
