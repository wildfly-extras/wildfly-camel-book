## Camel Deployment Configuration

If you want to fine tune the default configuration of your Camel deployment, you can edit either the `WEB-INF/jboss-all.xml` or `META-INF/jboss-all.xml` configuration file in your deployment.

You can set a `<camel-integration>` XML element to configure the Camel integration settings.  If you add
nested `<component>` or `<component-module>` XML elements, then instead of adding the default list of Camel components to your deployment, only the specified components will be added to your deployment.

Example `WEB-INF/jboss-all.xml` file:

    <?xml version="1.0" encoding="UTF-8"?>
    <jboss umlns="urn:jboss:1.0">
      <camel-integration xmlns="http://www.jboss.com/xml/ns/camel-integration">

        <component name="camel-ftp"/>
        <component-module name="org.apache.camel.component.rss"/>

      </camel-integration>
    </jboss>