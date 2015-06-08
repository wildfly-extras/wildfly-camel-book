## camel-salesforce

The [camel-salesforce](http://camel.apache.org/salesforce.html) component  provides producer and consumer endpoints to communicate with [Salesforce](http://www.salesforce.com/).

In order to use the component, you will need a valid Salesforce account together with credentials for accessing the Salesforce API:

* Client ID
* Client Secret
* Password
* Username

You'll want to generate the necessary Salesforce DTO objects before starting. This can be automated with the [Camel Salesforce Maven plugin](https://github.com/apache/camel/tree/camel-2.15.2/components/camel-salesforce/camel-salesforce-maven-plugin).

```
mvn camel-salesforce:generate -DcamelSalesforce.clientId=<clientid> -DcamelSalesforce.clientSecret=<clientsecret> -DcamelSalesforce.userName=<username> -DcamelSalesforce.password=<password>
```

This example queries Salesforce 'Opportunity' objects.
```java

SalesforceLoginConfig loginConfig = new SalesforceLoginConfig();
loginConfig.setClientId("your-client-id");
loginConfig.setPassword("your-password");
loginConfig.setClientSecret("your-client-secret");
loginConfig.setUserName("your-username");

SalesforceComponent component = new SalesforceComponent();
// The package path to the generated DTO classes
component.setPackages("org.wildfly.camel.test.salesforce.dto");
component.setLoginConfig(loginConfig);

CamelContext camelctx = new DefaultCamelContext();
camelctx.addComponent("salesforce",  component);
camelctx.addRoutes(new RouteBuilder() {
    @Override
    public void configure() throws Exception {
        from("direct:query")
        .to("salesforce:query?sObjectQuery=SELECT id,name from Opportunity&sObjectClass=" + QueryRecordsOpportunity.class.getName())
        .to("log:salesforce?showAll=true");
    }
});

camelctx.start();

ProducerTemplate producer = ctx.createProducerTemplate();
QueryRecordsOpportunity queryRecords = producer.requestBody("direct:query", null, QueryRecordsOpportunity.class);

```
