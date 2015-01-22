# camel-mail

Interaction with email is provided by the [camel-email component](http://camel.apache.org/mail.html).

To poll a mailbox for messages using POP3 you could do something like:

```java
CamelContext camelContext = new DefaultCamelContext();

camelContext.addRoutes(new RouteBuilder() {
    @Override
    public void configure() throws Exception {
        from("pop3://myuser@myhost?password=secret&consumer.delay=1000")
        .to("direct:email");
    }
});
```

To send an email using SMTP:

```java
CamelContext camelContext = new DefaultCamelContext();

camelContext.addRoutes(new RouteBuilder() {
  @Override
  public void configure() throws Exception {
      from("direct:start")
      .to("smtp://myuser@myhost?password=secretfrom=bob@myhost&to=kermit@myhost&subject=Greetings");
  }
});

ProducerTemplate producer = camelContext.createProducerTemplate();
producer.sendBody("direct:start", "Hello Kermit");
```
