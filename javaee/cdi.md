## Integration with CDI

CDI integration is provided by [camel-cdi](http://camel.apache.org/cdi.html).

A Context with an associated route can be provided like this

```java
@Startup
@ApplicationScoped
@ContextName("cdi-context")
public class MyRouteBuilder extends RouteBuilder {

    @Override
    public void configure() throws Exception {
    	from("direct:start").transform(body().prepend("Hi"));
    }
}
```

and consumed like this

```java
	@Inject
	@ContextName("cdi-context")
	private CamelContext camelctx;
```