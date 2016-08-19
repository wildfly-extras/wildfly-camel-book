# Camel Components

This chapter details information about supported camel components

## Supported Components

The following lists the set of supported Camel components that are available to JavaEE deployments:

* [camel-activemq](camel-activemq.md)
* [camel-atom](camel-atom.md)
* [camel-bean-validator](camel-bean-validator.md)
* [camel-bindy](camel-bindy.md)
* [camel-braintree](camel-braintree.md)
* [camel-cdi](camel-cdi.md)
* [camel-crypto](camel-crypto.md)
* [camel-cxf](camel-cxf.md)
* [camel-dns](camel-dns.md)
* [camel-dozer](camel-dozer.md)
* [camel-ejb](camel-ejb.md)
* [camel-elasticsearch](camel-elasticsearch.md)
* [camel-file2](camel-file2.md)
* [camel-ftp](camel-ftp.md)
* [camel-hl7](camel-hl7.md)
* [camel-http4](camel-http4.md)
* [camel-infinispan](camel-infinispan.md)
* [camel-jasypt](camel-jasypt.md)
* [camel-jaxb](camel-jaxb.md)
* [camel-jgroups](camel-jgroups.md)
* [camel-jms](camel-jms.md)
* [camel-jmx](camel-jmx.md)
* [camel-jpa](camel-jpa.md)
* [camel-kafka](camel-kafka.md)
* [camel-ldap](camel-ldap.md)
* [camel-linkedin](camel-linkedin.md)
* [camel-lucene](camel-lucene.md)
* [camel-mail](camel-mail.md)
* [camel-metrics](camel-metrics.md)
* [camel-mina2](camel-mina2.md)
* [camel-mqtt](camel-mqtt.md)
* [camel-mvel](camel-mvel.md)
* [camel-nats](camel-nats.md)
* [camel-netty4](camel-netty4.md)
* [camel-ognl](camel-ognl.md)
* [camel-quartz2](camel-quartz2.md)
* [camel-rest](camel-rest.md)
* [camel-restlet](camel-restlet.md)
* [camel-rss](camel-rss.md)
* [camel-salesforce](camel-salesforce.md)
* [camel-sap](camel-sap.md)
* [camel-saxon](camel-saxon.md)
* [camel-script](camel-script.md)
* [camel-servlet](camel-servlet.md)
* [camel-smpp](camel-smpp.md)
* [camel-sql](camel-sql.md)
* [camel-spring](camel-spring.md)
* [camel-stream](camel-stream.md)
* [camel-swagger](camel-swagger.md)
* [camel-twitter](camel-twitter.md)
* [camel-undertow](camel-undertow.md)
* [camel-velocity](camel-velocity.md)
* [camel-weather](camel-weather.md)
* [camel-xstream](camel-xstream.md)
* [camel-zookeeper](camel-zookeeper.md)

## Adding Components

Adding support for additional Camel Components is easy

#### Add your modules.xml definition

A modules.xml descriptor defines the class loading behavior for your component. It should be placed together with the component's jar in `modules/system/layers/fuse/org/apache/camel/component`. Module dependencies should be setup for direct compile time dependencies.

Here is an example for the camel-ftp component

```xml
<module xmlns="urn:jboss:module:1.1" name="org.apache.camel.component.ftp">
  <resources>
    <resource-root path="camel-ftp-2.14.0.jar" />
  </resources>
  <dependencies>
    <module name="com.jcraft.jsch" />
    <module name="javax.xml.bind.api" />
    <module name="org.apache.camel.core" />
    <module name="org.apache.commons.net" />
  </dependencies>
</module>
```

Please make sure you don't duplicate modules that are already available in WildFly and can be reused.

#### Add a reference to the component

To make this module visible by default to arbitrary JavaEE deployments add a reference to `modules/system/layers/fuse/org/apache/camel/component/main/module.xml`

```xml
<module xmlns="urn:jboss:module:1.3" name="org.apache.camel.component">

  <dependencies>
    ...
    <module name="org.apache.camel.component.ftp" export="true" services="export"/>
  </dependencies>

</module>
```
