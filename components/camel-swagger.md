## camel-swagger

The [camel-swagger](http://camel.apache.org/swagger-java.html) module is used for exposing the REST services and their APIs using [Swagger](http://swagger.io/).

```java
CamelContext camelctx = new DefaultCamelContext();
camelctx.addRoutes(new RouteBuilder() {
    @Override
    public void configure() throws Exception {
        restConfiguration().component("servlet").contextPath("swagger-tests/rest").port(8080);
        rest("/hello").get("/{name}").to("direct:hello");
        from("direct:hello").transform(simple("Hello ${header.name}"));
    }
});
camelctx.start();
```
