## Camel Context Deployments

There are two ways to deploy a Camel Context to WildFly

1. As a single XMl file with a predefined **-camel-context.xml** file suffix
2. As part of another WildFly supported deployment as **META-INF/jboss-camel-context.xml** file

When deployed as XML file, you should see

```
10:20:01,621 INFO  [org.jboss.as.server.deployment] (MSC service thread 1-3) JBAS015876: Starting deployment of "simple-transform-camel-context.xml"
...
10:20:01,893 INFO  [org.apache.camel.spring.SpringCamelContext] (MSC service thread 1-1) Apache Camel 2.11.0 (CamelContext: spring-context) is starting
...
10:20:01,945 INFO  [org.apache.camel.spring.SpringCamelContext] (MSC service thread 1-1) Route: route13 started and consuming from: Endpoint[direct://start]
10:20:01,949 INFO  [org.apache.camel.spring.SpringCamelContext] (MSC service thread 1-1) Apache Camel 2.11.0 (CamelContext: spring-context) started in 0.056 seconds
10:20:01,955 INFO  [org.wildfly.camel] (MSC service thread 1-1) JBAS020001: Register camel context: spring-context
...
10:20:01,963 INFO  [org.jboss.as.server] (management-handler-thread - 7) JBAS018559: Deployed "simple-transform-camel-context.xml" (runtime-name : "simple-transform-camel-context.xml")
```

When deployed as part of another deployment, you should something similar

```
10:24:02,649 INFO  [org.jboss.as.server.deployment] (MSC service thread 1-6) JBAS015876: Starting deployment of "camel-module.jar"
...
10:24:02,882 INFO  [org.apache.camel.spring.SpringCamelContext] (MSC service thread 1-1) Apache Camel 2.11.0 (CamelContext: spring-context) is starting
...
10:24:02,935 INFO  [org.apache.camel.spring.SpringCamelContext] (MSC service thread 1-1) Route: route14 started and consuming from: Endpoint[direct://start]
10:24:02,940 INFO  [org.apache.camel.spring.SpringCamelContext] (MSC service thread 1-1) Apache Camel 2.11.0 (CamelContext: spring-context) started in 0.058 seconds
10:24:02,945 INFO  [org.wildfly.camel] (MSC service thread 1-1) JBAS020001: Register camel context: spring-context
...
10:24:02,952 INFO  [org.jboss.as.server] (management-handler-thread - 11) JBAS018559: Deployed "camel-module.jar" (runtime-name : "camel-module.jar")
```
