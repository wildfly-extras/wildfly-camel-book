Camel supports the notion of Camel Context associated [RoutePolicies](http://camel.apache.org/routepolicy.html), which can be used to integrate with the WildFly security system.
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
