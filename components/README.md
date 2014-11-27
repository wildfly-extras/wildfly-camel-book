# Camel Components

This chapter details information about supported camel components

* [camel-cdi](components/camel-cdi.md)
* [camel-cxf](components/camel-cxf.md)
* [camel-jaxb](components/camel-jaxb.md)
* [camel-jms](components/camel-jms.md)
* [camel-jmx](components/camel-jmx.md)
* [camel-jpa](components/camel-jpa.md)

## Adding Components

Adding support for additional Camel Components is easy

1\. Add your modules.xml definition 

A modules.xml descriptor defines the class loading behavior for your component. It should be placed together with the component's jar in `modules/org/apache/camel/component`. Module dependencies should be setup for direct compile time dependencies. 

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



2\. Add a reference to the component 
