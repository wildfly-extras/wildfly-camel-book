## camel-braintree

The [camel-braintree](http://camel.apache.org/braintree.html) component provides access to Braintree Payments trough through their [Java SDK](https://developers.braintreepayments.com/start/hello-server/java).

```java
BraintreeComponent component = new BraintreeComponent(camelctx);
component.setConfiguration(configuration);
camelctx.addComponent("braintree", component);

camelctx.addRoutes(new RouteBuilder() {
    @Override
    public void configure() throws Exception {
        from("timer://braintree?repeatCount=1")
            .to("braintree:clientToken/generate")
            .process(new Processor() {
                @Override
                public void process(Exchange exchange) throws Exception {
                    latch.countDown();
                }})
            .to("mock:result");
    }
});
```
