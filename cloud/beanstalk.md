## AWS Elastic Beanstalk

WildFly Camel also comes as [docker image](https://registry.hub.docker.com/u/wildflyext/wildfly-camel/). This allows you to run the certified WildFly JavaEE server with Camel Integration in any managed environment that supports [Docker](https://www.docker.com/).

Here is an easy 3-Step process to run WildFly Camel on Elastic Beanstalk

### Define the Dockerrun.aws.json

A ```Dockerrun.aws.json``` file describes how to deploy a Docker container as an AWS Elastic Beanstalk application.

Here is a simple example that uses a plain wildfly-camel distro image

```json
{
  "AWSEBDockerrunVersion": "1",
  "Image": {
    "Name": "wildflyext/wildfly-camel",
    "Update": "false"
  },
  "Ports": [
    {
      "ContainerPort": "8080"
    }
  ]
}
```

### Create the Elastic Beanstalk Application

Navigate to [Elastic Beanstalk](https://eu-west-1.console.aws.amazon.com/elasticbeanstalk/home?region=eu-west-1) and start creating an application.

![create app](../images/beanstalk-step-00.png)

Select the environment tpe. In this case we use a simple WebServer tier with docker as a single instance.

![env type][../images/beanstalk-step-01.png]

Upload the ```Dockerrun.aws.json``` file from above.

![app version][beanstalk-step-02]

Give the environment a name.

![env name][beanstalk-step-03]

Add more configuration. Here I select my EC2 key pair so that I can SSH into the EC2 instance if needed.

![conf details][beanstalk-step-05]

Launch the the application - this may take few minutes.

![launch][beanstalk-step-final]

### Accessing the WildFly Camel Application

Finally, you should be able to access the wildfly-camel application on http://wildfly-camel.elasticbeanstalk.com.

The [Hawt.io](http://hawt.io/) console should show the camel context from subsystem configuration.

![hawtio][beanstalk-hawtio-camel]

[beanstalk-step-00]: 

[beanstalk-step-01]: https://raw.githubusercontent.com/wildflyext/wildfly-camel-book/master/images/beanstalk-step-01.png

[beanstalk-step-02]: https://raw.githubusercontent.com/wildflyext/wildfly-camel-book/master/images/beanstalk-step-02.png

[beanstalk-step-03]: https://raw.githubusercontent.com/wildflyext/wildfly-camel-book/master/images/beanstalk-step-03.png

[beanstalk-step-05]: https://raw.githubusercontent.com/wildflyext/wildfly-camel-book/master/images/beanstalk-step-05.png

[beanstalk-step-final]: https://raw.githubusercontent.com/wildflyext/wildfly-camel-book/master/images/beanstalk-step-final.png

[beanstalk-hawtio-camel]: https://raw.githubusercontent.com/wildflyext/wildfly-camel-book/master/images/beanstalk-hawtio-camel-01.png
