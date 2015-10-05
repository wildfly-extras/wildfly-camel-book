## camel-elasticsearch

The [camel-elasticsearch](http://camel.apache.org/elasticsearch.html) component allows you to interface with an [ElasticSearch](https://www.elastic.co/) server.

```java
   CamelContext camelContext = new DefaultCamelContext();
   camelContext.addRoutes(new RouteBuilder() {
       @Override
       public void configure() throws Exception {
           from("direct:index")
           .to("elasticsearch://local?operation=INDEX&indexName=twitter&indexType=tweet");
       }
   });

   Map<String, String> indexedData = new HashMap<>();
   indexedData.put("content", "test");

   ProducerTemplate template = camelContext.createProducerTemplate();
   template.sendBody("direct:index", indexedData);
```
