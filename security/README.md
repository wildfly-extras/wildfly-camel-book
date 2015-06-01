# Security

Security in WildFly is a broad topic. Both WildFly and camel have well documented, stadardised methods of securing configuration, endpoints and payloads. Camel supports the notion of Camel Context associated Policies, which can be used to integrate with the WildFly security system.

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

Please also see the security related documentation for these endpoints:

* [JAX-RS Security](jaxrs.md)
* [JAX-WS Security](jaxws.md)
* [JMS Security](jms.md)
