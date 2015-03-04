## camel-dozer

The [camel-dozer](http://camel.apache.org/dozer.html) component provides the ability to map between Java beans using the [Dozer](http://camel.apache.org/dozer-type-conversion.html) mapping framework.  Camel also supports the ability to trigger Dozer mappings as a [type converter](http://camel.apache.org/dozer-type-conversion.html).

```java
    CamelContext camelctx = new DefaultCamelContext();
    camelctx.addRoutes(new RouteBuilder() {
        @Override
        public void configure() throws Exception {
            from("direct:start").convertBodyTo(CustomerB.class);
        }
    });

    DozerBeanMapperConfiguration mconfig = new DozerBeanMapperConfiguration();
    mconfig.setMappingFiles(Arrays.asList(new String[] { "mappings.xml" }));
    new DozerTypeConverterLoader(camelctx, mconfig);
```

