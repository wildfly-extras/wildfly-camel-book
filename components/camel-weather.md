# camel-weather

Integration with the [Open Weather Map API](http://openweathermap.org/api) is provided by the [camel-weather](http://camel.apache.org/weather.html) component.

As an example, we can consume the current weather for Madrid in Spain and make some decisions based upon the humidity percentage:

```java
CamelContext camelContext = new DefaultCamelContext();

camelContext.addRoutes(new RouteBuilder() {
    @Override
    public void configure() throws Exception {
        from("weather:foo?location=Madrid,Spain")
        .choice()
          .when().jsonpath("$..[?(@humidity > 90)]")
            .to("direct:veryhumid")
          .when().jsonpath("$..[?(@humidity > 70)]")
            .to("direct:humid")
          .otherwise()
            .to("direct:nothumid");
    }
});

camelContext.start();
```
