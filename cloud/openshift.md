## OpenShift Origin

This chapter gets you started on WildFly-Camel in [OpenShift Origin](https://github.com/openshift/origin)  


### Starting OpenShift

We can start OpenShift Origin like this

```
$ docker run --rm --name openshift-origin --net=host --privileged -v /var/run/docker.sock:/var/run/docker.sock -v /tmp/openshift:/tmp/openshift wildflyext/openshift start --cert-dir=/tmp/openshift
```

Then verify the OpenShift version

```
$ docker run --rm wildflyext/openshift version
openshift version 0.1, build c322309
kubernetes v0.8.0-dev
```

We may also want to create an alias to OpenShift

```
$ alias openshift-cli="docker run --rm --net=host -v /tmp/openshift:/tmp/openshift wildflyext/openshift cli --kubeconfig=/tmp/openshift/admin/.kubeconfig"
```

## Standalone Servers

Here we run a set of WildFly Camel servers on [OpenShift Origin](https://github.com/openshift/origin). The target platform is Linux on an Amazon EC2 instance. When done, we have a set of portable [Docker](https://www.docker.io/) containers that can be deployed onto a production platform.

![](../images/example-rest-design.png)

The example architecture consists of a set of three high available (HA) servers running REST endpoints. For server replication and failover we use [Kubernetes](http://kubernetes.io). Each server runs in a dedicated [Pod](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/pods.md) that we access via [Services](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/services.md).


### Running a single Pod

A simple Pod configuration for a WildFly Camel container might be defined as in [wildfly-camel-step01.json](../sources/wildfly-camel-step01.json)

To create the Pod in OpenShift we do

```
$ openshift-cli apply -f https://raw.githubusercontent.com/wildfly-extras/wildfly-camel-book/2.2/sources/wildfly-camel-step01.json
I1203 11:58:28.876288 00001 kubecfg.go:613] Creation succeeded for Pod with name camel-pod
```

You can see the running Pod like this

```
$ openshift-cli get pods -l name=camel
Name                Image(s)                        Host                Labels                Status
----------          ----------                      ----------          ----------            ----------
camel-pod           wildflyext/example-camel-rest   ip-172-30-0-233/    name=camel,role=pod   Running
```

and delete it again with

```
$ openshift-cli delete pod -l name=camel
```

### Adding a ReplicationController

To achieve high availability (HA), lets replicate this Pod using a ReplicationController as in [wildfly-camel-step02.json](../sources/wildfly-camel-step02.json)

To create the replicated Pod in OpenShift we do

```
$ openshift-cli apply -f https://raw.githubusercontent.com/wildfly-extras/wildfly-camel-book/2.2/sources/wildfly-camel-step02.json
I1203 13:19:56.780955 00001 kubecfg.go:613] Creation succeeded for ReplicationController with name restSlaveController
```

We now have three Pods each running an instance of our container

```
$ openshift-cli get pods
Name                                   Image(s)                        Host                Labels              Status
----------                             ----------                      ----------          ----------          ----------
13520066-7aef-11e4-9ea2-0624f808fac8   wildflyext/example-camel-rest   ip-172-30-0-233/    name=camel          Running
1351d659-7aef-11e4-9ea2-0624f808fac8   wildflyext/example-camel-rest   ip-172-30-0-233/    name=camel          Running
1352aa99-7aef-11e4-9ea2-0624f808fac8   wildflyext/example-camel-rest   ip-172-30-0-233/    name=camel          Running

```

### Adding a Service

The entry point into the system is a Kubernetes Service as in [wildfly-camel-step03.json](../sources/wildfly-camel-step03.json)

To create a Service that accesses replicated Pods do

```
$ openshift-cli apply -f https://raw.githubusercontent.com/wildfly-extras/wildfly-camel-book/2.2/sources/wildfly-camel-step03.json
I1203 14:28:44.860519 00001 kubecfg.go:613] Creation succeeded for Service with name rest-service
I1203 14:28:44.860770 00001 kubecfg.go:613] Creation succeeded for ReplicationController with name rest-controller
```

We now have a service

```
$ openshift-cli get services -l name=camel-srv
Name                Labels              Selector            IP                  Port
----------          ----------          ----------          ----------          ----------
rest-service        name=camel-srv      name=camel-pod      172.121.17.3        8080
```

> <small>Note, this uses a hard coded mapping in [wildfly-camel-step03.json](../sources/wildfly-camel-step03.json) for publicIPs, which would have to be replaced according to your EC2 setup.</small>

From a remote client, you should now be able to access the service like this

```
$ curl http://54.154.239.169:8080/example-camel-rest/rest/greet/hello/Kermit
Hello Kermit from 172.17.0.51
```

## Domain Setup

Running multiple server containers in a cloud environment is often only useful when these containers can also be managed. In the previous example we had three servers that each exposed an HTTP service reachable through a Kubernetes [Service](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/services.md). The management interface of these servers were not exposed. It would be virtually impossible to adjust configurations for these individual servers or the whole set. As a minimum we would like to monitor the health state of these servers so that we can possibly replace containers if needed.

![](../images/example-domain-design.png)


### Starting the Domain

The WildFly-Camel domain can be configured as in [wildfly-camel-domain.json](../sources/wildfly-camel-domain.json)

```
$ openshift-cli apply -f https://raw.githubusercontent.com/wildfly-extras/wildfly-camel-book/2.2/sources/wildfly-camel-domain.json
I1216 10:47:51.071633       1 kubecfg.go:613] Creation succeeded for Service with name management-service
I1216 10:47:51.071747       1 kubecfg.go:613] Creation succeeded for Service with name domain-controller
I1216 10:47:51.071758       1 kubecfg.go:613] Creation succeeded for Service with name rest-service
I1216 10:47:51.071761       1 kubecfg.go:613] Creation succeeded for ReplicationController with name master-replicator
I1216 10:47:51.071764       1 kubecfg.go:613] Creation succeeded for ReplicationController with name host-replicator

```

and verify the resulting servies like that
```
$ openshift-cli get services
Name                 Labels              Selector                                  IP                  Port
----------           ----------          ----------                                ----------          ----------
kubernetes                               component=apiserver,provider=kubernetes   172.121.17.213      443
kubernetes-ro                            component=apiserver,provider=kubernetes   172.121.17.239      80
management-service                       name=ctrl-pod                             172.121.17.49       9990
domain-controller                        name=ctrl-pod                             172.121.17.110      9999
rest-service                             name=http-pod                             172.121.17.60       8080
```
Now, you should be able to access the admin console like this: http://54.154.82.232:9990/console

![](../images/console-domain.png)

We believe that managing deployments through the WildFly admin interface does not make much sense. Instead, deployments should already be backed into containers that you spin up in the various Pods. 

There is a wide spectrum of opinion on whether this also applies to configuration. Here we retain the WildFly domain configurability (i.e. mutable containers for configuration). 

Feedback is welcome.
