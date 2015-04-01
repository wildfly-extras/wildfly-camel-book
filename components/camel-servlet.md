## camel-servlet

The [camel-servlet](http://camel.apache.org/servlet.html) component provides HTTP based endpoints for consuming HTTP requests that arrive at a HTTP endpoint that is bound to a published Servlet.

```java
    CamelContext camelctx = new DefaultCamelContext();
    camelctx.addRoutes(new RouteBuilder() {
        @Override
        public void configure() throws Exception {
            from("servlet://hello?servletName=CamelServletTest&matchOnUriPrefix=true")
            .process(new Processor() {
                @Override
                public void process(Exchange exchange) throws Exception {
                    exchange.getOut().setBody("Hello Kermit");
                }
            });
        }
    });
```
