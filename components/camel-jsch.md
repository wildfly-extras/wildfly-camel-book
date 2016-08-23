## camel-jsch

The [camel-jsch](http://camel.apache.org/jsch.html) component enables secure file transfer using the [SCP protocol](http://en.wikipedia.org/wiki/Secure_copy).

```java
CamelContext camelctx = new DefaultCamelContext();
Endpoint endpoint = camelctx.getEndpoint("scp://localhost:22/my/directory?username=admin&password=admin");

camelctx.createProducerTemplate().sendBodyAndHeader(endpoint, "Hello", "CamelFileName", "test.txt");
```
