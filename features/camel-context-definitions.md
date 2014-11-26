## Camel Context Definitions

Camel Contexts can be configured in standalone-camel.xml as part of the subsystem definition like this

```xml
<subsystem xmlns="urn:jboss:domain:camel:1.0">
   <camelContext id="system-context-1">
     <![CDATA[
     <route>
       <from uri="direct:start"/>
       <transform>
         <simple>Hello #{body}</simple>
       </transform>
     </route>
     ]]>
   </camelContext>
</subsystem>
```

On WildFly startup you should see something like this

```
10:01:29,213 INFO  [org.wildfly.camel] (MSC service thread 1-7) JBAS020001: Register camel context: system-context-1
10:01:29,214 INFO  [org.wildfly.camel] (MSC service thread 1-1) JBAS020002: Bound camel naming object: java:jboss/camel/CamelContextRegistry
```
