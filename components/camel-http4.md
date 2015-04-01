## camel-http

The [camel-http](http://camel.apache.org/http4.html) component provides HTTP based endpoints for calling external HTTP resources.

```java
    CamelContext camelctx = new DefaultCamelContext();
    camelctx.addRoutes(new RouteBuilder() {
        @Override
        public void configure() throws Exception {
            from("direct:start")
            .to("http4://somehost:8080/simple/myservlet");
        }
    });
```
