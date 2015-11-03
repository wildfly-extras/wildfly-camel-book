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

### Spring JDBC XML namespace support

Support for the following Spring JDBC XML configurations is supported

__jdbc:embedded-database__

> NOTE: Only H2 databases are supported by default as WildFly has native support for this. If you want to use other embedded database providers, you will need
to install the appropriate database driver.

```xml
<jdbc:embedded-database id="datasource" type="H2">
  <jdbc:script location="db-schema.sql"/>
</jdbc:embedded-database>
```

__jdbc:initialize-database__

```xml
<jdbc:initialize-database data-source="datasource">
  <jdbc:script location="classpath:db-init.sql"/>
</jdbc:initialize-database>
```
