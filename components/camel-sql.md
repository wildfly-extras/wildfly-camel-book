## camel-sql

The [camel-sql](http://camel.apache.org/sql-component.html) component allows you to work with databases using [JDBC](http://camel.apache.org/jdbc.html) queries. The difference between this component and JDBC component is that in case of SQL the query is a property of the endpoint and it uses message payload as parameters passed to the query.

```java
    CamelContext camelctx = new DefaultCamelContext();
    camelctx.addRoutes(new RouteBuilder() {
        @Override
        public void configure() throws Exception {
            from("sql:select name from information_schema.users?dataSource=java:jboss/datasources/ExampleDS")
            .to("direct:end");
        }
    });
```
