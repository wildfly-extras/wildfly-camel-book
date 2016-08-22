## camel-snakeyaml

The [camel-snakeyaml](http://camel.apache.org/yaml-data-format.html) component provides the capabilty to marshal and unmarshal Java objects with [YAML](http://www.yaml.org/).

### Marshal POJO to YAML
```java
CamelContext camelctx = new DefaultCamelContext();
camelctx.addRoutes(new RouteBuilder() {
    @Override
    public void configure() throws Exception {
        from("direct:start")
        .marshal().yaml(YAMLLibrary.SnakeYAML)
    }
});

camelctx.start();
try {
    ProducerTemplate producer = camelctx.createProducerTemplate();
    String customerYAML = producer.requestBody("direct:start", new Customer("John", "Doe"), String.class);
} finally {
    camelctx.stop();
}
```

### Unmarshal YAML to POJO
```java
CamelContext camelctx = new DefaultCamelContext();
camelctx.addRoutes(new RouteBuilder() {
    @Override
    public void configure() throws Exception {
        from("direct:start")
        .unmarshal().yaml(YAMLLibrary.SnakeYAML)
    }
});

camelctx.start();
try {
    ProducerTemplate producer = camelctx.createProducerTemplate();
    Customer customer = producer.requestBody("direct:start", "!!org.mycompany.Customer {firstName: John, lastName: Doe}", Customer.class);
} finally {
    camelctx.stop();
}
```
