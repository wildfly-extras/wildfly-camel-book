## camel-restlet

The [camel-restlet](http://camel.apache.org/restlet.html) component allows you to define REST producer endpoints to consume JAX-RS REST services.

> **IMPORTANT: At present the WildFly Camel Subsytem does not support Restlet consumers. E.g endpoints defined as from("restlet://"). Other JAX-RS consumer endpoint solutions are described in the [JAX-RS documentation](../javaee/jaxrs.md).**

```java
    CamelContext camelctx = new DefaultCamelContext();
    camelctx.addRoutes(new RouteBuilder() {
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
    });
```
