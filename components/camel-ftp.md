# camel-ftp

The [camel-ftp](http://camel.apache.org/ftp2.html) component provides access to remote file systems over the FTP and SFTP protocols.

```java
CamelContext ctx = new DefaultCamelContext();
Endpoint endpoint = ctx.getEndpoint("ftp://localhost:21000/foo?username=admin&password=admin");

ctx.createProducerTemplate().sendBodyAndHeader(endpoint, "Hello", "CamelFileName", "test.txt");
```