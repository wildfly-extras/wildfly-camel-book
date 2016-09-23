## Source To Image

WildFly-Camel provides a [Source-To-Image](https://docs.openshift.org/latest/architecture/core_concepts/builds_and_image_streams.html#source-build)(S2I) [Docker image](https://hub.docker.com/r/wildflyext/s2i-wildfly-camel/) which enables you to build reproducable images from source.

### Example S2I build

First you'll need to download and install the S2I tooling from [here](https://github.com/openshift/source-to-image).

Then you can build and run a Docker image from source. For example, to build the WildFly-Camel CDI quickstart:

```
$ s2i build https://github.com/wildfly-extras/wildfly-camel-examples.git -r {{ book.version }} --context-dir camel-cdi wildflyext/s2i-wildfly-camel:{{ book.version }} wildfly-camel-example-cdi

$ docker run -p 8080:8080 wildfly-camel-example-cdi
```

### S2I with OpenShift Origin

For convenience, WildFly-Camel provides an OpenShift [ImageStream](https://raw.githubusercontent.com/wildfly-extras/wildfly-camel-book/{{ book.version }}/sources/wildfly-camel-image-streams.json) template and an [application template](https://raw.githubusercontent.com/wildfly-extras/wildfly-camel-book/{{ book.version }}/sources/wildfly-camel-template.json) which enable you to to perform S2I build and deployments in OpenShift origin.

First add the WildFly-Camel S2I ImageStream.
```
$ oc apply -f https://raw.githubusercontent.com/wildfly-extras/wildfly-camel-book/{{ book.version }}/sources/wildfly-camel-image-streams.json
```

Then add the application template.
```
$ oc apply -f https://raw.githubusercontent.com/wildfly-extras/wildfly-camel-book/{{ book.version }}/sources/wildfly-camel-template.json
```

When you select 'Add to Project' in the OpenShift web console you should see a new catalog item named
's2i-wildfly-camel'. Choose this option, provide the required template parameters and click 'Create' to start the build process.

![](../images/wildfly-camel-s2i.png)

You'll then be able to monitor your application S2I build and deployment in OpenShift. Eventually you should see your application with 1 pod running.

![](../images/wildfly-camel-s2i-deployed.png)
