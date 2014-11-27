## Integration with JNDI

JNDI integration is provided by a WildFly specific CamelContext, which is can be obtained like this

```java
InitialContext inictx = new InitialContext();
CamelContextFactory contextFactory = inictx.lookup(CamelConstants.CAMEL_CONTEXT_FACTORY_BINDING_NAME);

WildFlyCamelContext camelctx = contextFactory.createCamelContext();

```