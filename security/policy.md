# Route Policy

Camel supports the notion of [RoutePolicies](http://camel.apache.org/routepolicy.html), which can be used to integrate with the WildFly security system. There are currently two supported scenarios for security integration.

## Camel calls into JavaEE

When a camel route calls into a secured JavaEE component, it acts as a client and must provide appropriate credentials associated with the call.

The route can be decorated with a `ClientAuthorizationPolicy`

```
CamelContext camelctx = new DefaultCamelContext();
camelctx.addRoutes(new RouteBuilder() {
    @Override
    public void configure() throws Exception {
        from("direct:start")
        .policy(new ClientAuthorizationPolicy())
        .to("ejb:java:module/AnnotatedSLSB?method=doSelected");
    }
});
```


## JavaEE calls into Camel

In the example below we associate a simple AuthorizationPolicy for username/password authentication with the Route to secure access to an EJB3 endpoint.

```java
CamelContext camelctx = new DefaultCamelContext();
camelctx.addRoutes(new RouteBuilder() {
    @Override
    public void configure() throws Exception {
        from("direct:start")
        .policy(new AuthorizationPolicy(EJBSecurityTestCase.USERNAME, EJBSecurityTestCase.PASSWORD))
        .to("ejb:java:module/AnnotatedSLSB?method=doSelected");
    }
});
camelctx.start();
```
