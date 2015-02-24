## Camel Deployment Configuration

If you want to fine tune the default configuration of your Camel deployment, you can edit either the `WEB-INF/jboss-all.xml` or `META-INF/jboss-all.xml` configuration file in your deployment.

Use a `<camel-integration>` XML element within the `jboss-all.xml` file to control the camel configuration.

### Disabling the Camel Subsystem

If you don't want the camel subsystem to be imported into
your deployment, add the `.

Example `jboss-all.xml` file:

    <?xml version="1.0" encoding="UTF-8"?>
    <jboss umlns="urn:jboss:1.0">
      <camel-integration enabled="false" xmlns="http://www.jboss.com/xml/ns/camel-integration"/>
    </jboss>
    
### Changing the Camel Components added to the Deployment

If you add nested `<component>` or `<component-module>` XML elements, then instead of adding the default list of Camel components to your deployment, only the specified components will be added to your deployment.

Example `jboss-all.xml` file:

    <?xml version="1.0" encoding="UTF-8"?>
    <jboss umlns="urn:jboss:1.0">
      <camel-integration xmlns="http://www.jboss.com/xml/ns/camel-integration">

        <component name="camel-ftp"/>
        <component-module name="org.apache.camel.component.rss"/>

      </camel-integration>
    </jboss>


    </jboss