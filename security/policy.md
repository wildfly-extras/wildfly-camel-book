# Route Policy

Camel supports the notion of [RoutePolicies](http://camel.apache.org/routepolicy.html), which can be used to integrate with the WildFly security system. There are currently two supported scenarios for security integration.

## Camel calls into JavaEE

When a camel route calls into a secured JavaEE component, it acts as a client and must provide appropriate credentials associated with the call.

The route can be decorated with a `ClientAuthorizationPolicy`

```java
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
This does actually no do any authentication/authorization as part of the Camel message processing. Instead it associates the credentials that come with the Camel Exchange with the call into the EJB3 layer.

The client that calls the message consumer must provide that appropriate credentials in the AUTHENTICATION header like this:

```java
ProducerTemplate producer = camelctx.createProducerTemplate();
Subject subject = new Subject();
subject.getPrincipals().add(new DomainPrincipal(domain));
subject.getPrincipals().add(new EncodedUsernamePasswordPrincipal(username, password));
producer.requestBodyAndHeader("direct:start", "Kermit", Exchange.AUTHENTICATION, subject, String.class);
```
Authentication and authorization will happen in the JavaEE layer.

## Securing a Camel Route

In order to secure a Camel Route, we can associate a `DomainAuthorizationPolicy` with the route. 


```java
CamelContext camelctx = new DefaultCamelContext();
camelctx.addRoutes(new RouteBuilder() {
    @Override
    public void configure() throws Exception {
        from("direct:start")
        .policy(new DomainAuthorizationPolicy().roles("Role2"))
        .transform(body().prepend("Hello "));
    }
});
camelctx.start();
```
