## camel-infinispan

This [camel-infinispan](http://camel.apache.org/infinispan.html) component allows you to interact with [Infinispan](http://infinispan.org) distributed data grid / caches.

You can interact with container managed Infinispan caches by referring to their JNDI lookup within your Camel route. For example, to write a cache entry.

```java
  DefaultCamelContext camelctx = new DefaultCamelContext();
  camelctx.addRoutes(new RouteBuilder() {
      @Override
      public void configure() throws Exception {
          from("direct:put")
          .to("infinispan://localhost?cacheContainer=#java:jboss/infinispan/container/myCache&command=PUT");
      }
  });

  Map<String, Object> headers = new HashMap<>();
  headers.put(InfinispanConstants.KEY, "name");
  headers.put(InfinispanConstants.VALUE, "kermit");

  ProducerTemplate template = camelctx.createProducerTemplate();
  template.sendBodyAndHeaders("direct:put", null, headers);
```

Or you can interact with external Infinispan caches.

```java
  DefaultCamelContext camelctx = new DefaultCamelContext();
  camelctx.addRoutes(new RouteBuilder() {
      @Override
      public void configure() throws Exception {
          from("direct:put")
          .to("infinispan://my.cache.host?command=PUT");
      }
  });

  Map<String, Object> headers = new HashMap<>();
  headers.put(InfinispanConstants.KEY, "name");
  headers.put(InfinispanConstants.VALUE, "kermit");

  ProducerTemplate template = camelctx.createProducerTemplate();
  template.sendBodyAndHeaders("direct:put", null, headers);
```
