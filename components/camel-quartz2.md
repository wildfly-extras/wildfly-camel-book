## camel-quartz

The [camel-quartz](http://camel.apache.org/quartz2.html) component provides a scheduled delivery of messages using the [Quartz Scheduler 2.x](http://www.quartz-scheduler.org). 

```java
    CamelContext camelctx = new DefaultCamelContext();
    camelctx.addRoutes(new RouteBuilder() {
        public void configure() throws Exception {
            from("quartz2://mytimer?trigger.repeatCount=3&trigger.repeatInterval=100")
            .process(new Processor() {
				public void process(Exchange exchange) throws Exception {
					latch.countDown();
				}})
            .to("mock:result");
        }
    });
```
