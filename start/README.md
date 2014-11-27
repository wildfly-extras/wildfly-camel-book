# Getting Started

This chapter takes you through the first steps of getting WildFly-Camel and provides the initial pointers to get up and running.

## Download the Distribution

WildFly Camel is distributed as

1. WildFly Patch - [wildfly-camel-patch](https://repository.jboss.org/nexus/content/groups/public-jboss/org/wildfly/camel/wildfly-camel-patch/)
2. Docker Image - [wildflyext/wildfly-camel](https://registry.hub.docker.com/u/wildflyext/wildfly-camel/)


## Installing the Camel Subsystem

Simply apply the patch to the respective wildfly version. For possible WildFly target versions, see the [compatibility page](compatibility.md)

## Standalone Server

In your WildFly home directory run ...

```
$ bin/standalone.sh -c standalone-camel.xml
...
10:50:02,833 INFO  [org.jboss.as.server] (ServerService Thread Pool -- 31) JBAS018559: Deployed "wildfly-camel.war" (runtime-name : "wildfly-camel.war")
10:50:02,834 INFO  [org.jboss.as.server] (ServerService Thread Pool -- 31) JBAS018559: Deployed "hawtio.war" (runtime-name : "hawtio.war")
10:50:02,848 INFO  [org.jboss.as] (Controller Boot Thread) JBAS015961: Http management interface listening on http://127.0.0.1:9990/management
10:50:02,849 INFO  [org.jboss.as] (Controller Boot Thread) JBAS015951: Admin console listening on http://127.0.0.1:9990
10:50:02,849 INFO  [org.jboss.as] (Controller Boot Thread) JBAS015874: WildFly 8.1.0.Final "Kenny" started in 10804ms
```

## Domain Mode

Similarly, for the WildFly Domain Mode run ...  

```
$ bin/domain.sh -c domain-camel.xml
```

## Docker Image

The easiest and most portable way to run WildFly-Camel is to use the [wildflyext/wildfly-camel](https://registry.hub.docker.com/u/wildflyext/wildfly-camel/) distribution. 

```
$ docker run -ti -p 8080:8080 wildflyext/wildfly-camel
```
