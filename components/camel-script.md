## camel-script

The [camel-script](http://camel.apache.org/scripting-languages.html) component supports a number of scripting languages which can be used to create an [Expression](http://camel.apache.org/expression.html) or [Predicate](http://camel.apache.org/predicate.html) via the standard [JSR 223](http://jcp.org/en/jsr/detail?id=223)

```java
    CamelContext camelctx = new DefaultCamelContext();
    camelctx.addRoutes(new RouteBuilder() {
        @Override
        public void configure() throws Exception {
            from("direct:start").choice()
            .when(script("beanshell", "request.getHeaders().get(\"foo\").equals(\"bar\")")).to("mock:result")
            .otherwise().transform(body().append(" unmatched")).to("mock:unmatched");
        }
    });
```

