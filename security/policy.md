Camel supports the notion of [RoutePolicies](http://camel.apache.org/routepolicy.html), which can be used to integrate with the WildFly security system.

There are currently two supported scenarios for security integration:



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
