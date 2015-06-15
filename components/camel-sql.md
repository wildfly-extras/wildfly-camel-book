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

When used in conjunction with the [camel-cdi](camel-cdi.md) component, Java EE annotations can make a datasource available to Camel. This example uses the `@Named` annotation so that Camel can discover the desired datasource.

```java
public class DatasourceProducer {
    @Resource(name = "java:jboss/datasources/ExampleDS")
    DataSource dataSource;

    @Produces
    @Named("wildFlyExampleDS")
    public DataSource getDataSource() {
        return dataSource;
    }
}
```

Now the datasource can be referenced through the `dataSource` parameter on the camel-sql endpoint configuration.

```java
@ApplicationScoped
@ContextName("camel-sql-cdi-context")
@Startup
public class CdiRouteBuilder extends RouteBuilder {
    @Override
    public void configure() throws Exception {
        from("sql:select name from information_schema.users?dataSource=wildFlyExampleDS")
        .to("direct:end");
    }
}
```
