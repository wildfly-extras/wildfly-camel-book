## Domain Setup

In this guide we show how to run a WildFly Camel domain on [OpenShift V3](https://github.com/openshift/origin). The target platform is Linux on an Amazon EC2 instance. When done, we have a set of portable [Docker](https://www.docker.io/) containers that can be deployed onto a production platform.

Running multiple servers in a cloud environment is all the more useful when these servers can also be managed. In the [previous example](openshift-standalone.md) we had three servers that each exposed an HTTP service reachable through a Kubernetes Service.  
