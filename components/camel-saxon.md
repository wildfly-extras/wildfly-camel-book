## camel-saxon

The [camel-saxon](http://camel.apache.org/xquery.html) component supports [XQuery](http://www.w3.org/TR/xquery/) to allow an [Expression](http://camel.apache.org/expression.html) or [Predicate](http://camel.apache.org/predicate.html) to be used in the [DSL](http://camel.apache.org/dsl.html) or [Xml Configuration](http://camel.apache.org/xml-configuration.html).

```java
    CamelContext camelctx = new DefaultCamelContext();
    camelctx.addRoutes(new RouteBuilder() {
        @Override
        public void configure() throws Exception {
            Namespaces ns = new Namespaces("ns", "http://org/wildfly/test/jaxb/model/Customer");
            from("direct:start").transform().xquery("/ns:customer/ns:firstName", String.class, ns)
            .to("mock:result");
        }
    });
```
