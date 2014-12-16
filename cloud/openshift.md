## OpenShift Origin

This chapter gets you started on WildFly-Camel in [OpenShift Origin](https://github.com/openshift/origin)  


### Starting OpenShift 

We can start OpenShift Origin like this

```
$ docker run --rm -v /var/run/docker.sock:/var/run/docker.sock --net=host --privileged openshift/origin start
```

and verify the OpenShift version 

```
$ docker run --rm openshift/origin version
openshift version 0.1, build 725d9ca
kubernetes v0.4-dev
```

We may also want to create an alias to the Kubernetes client

```
alias kube="docker run --rm --net=host openshift/origin kube"
```

## Standalone Servers

Here we run a set of WildFly Camel servers on [OpenShift Origin](https://github.com/openshift/origin). The target platform is Linux on an Amazon EC2 instance. When done, we have a set of portable [Docker](https://www.docker.io/) containers that can be deployed onto a production platform.

![](../images/example-rest-design.png)

The example architecture consists of a set of three high available (HA) servers running REST endpoints. For server replication and failover we use [Kubernetes](http://kubernetes.io). Each server runs in a dedicated [Pod](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/pods.md) that we access via [Services](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/services.md).


### Running a single Pod

A simple Pod configuration for a WildFly Camel container might be defined as in [wildfly-camel-step01.json](../sources/wildfly-camel-step01.json)

To create the Pod in OpenShift we do

[TODO] use ref to master

```
$ kube apply -c https://raw.githubusercontent.com/wildfly-extras/wildfly-camel-book/2.1/sources/wildfly-camel-step01.json
I1203 11:58:28.876288 00001 kubecfg.go:613] Creation succeeded for Pod with name camel-pod
```

You can see the running Pod like this

```
$ kube get pods/camel-pod
Name                Image(s)                        Host                Labels                Status
----------          ----------                      ----------          ----------            ----------
camel-pod           wildflyext/example-camel-rest   ip-172-30-0-233/    name=camel,role=pod   Running
```

and delete it again with 

```
$ kube delete pods/camel-pod
```

### Adding a ReplicationController

To achieve high availability (HA), lets replicate this Pod using a ReplicationController as in [wildfly-camel-step02.json](../sources/wildfly-camel-step02.json)

To create the replicated Pod in OpenShift we do

[TODO] use ref to master

```
$ kube apply -c https://raw.githubusercontent.com/wildfly-extras/wildfly-camel-book/2.1/sources/wildfly-camel-step02.json
I1203 13:19:56.780955 00001 kubecfg.go:613] Creation succeeded for ReplicationController with name restSlaveController
```

We now have three Pods each running an instance of our container

```
$ kube list pods
Name                                   Image(s)                        Host                Labels              Status
----------                             ----------                      ----------          ----------          ----------
13520066-7aef-11e4-9ea2-0624f808fac8   wildflyext/example-camel-rest   ip-172-30-0-233/    name=camel          Running
1351d659-7aef-11e4-9ea2-0624f808fac8   wildflyext/example-camel-rest   ip-172-30-0-233/    name=camel          Running
1352aa99-7aef-11e4-9ea2-0624f808fac8   wildflyext/example-camel-rest   ip-172-30-0-233/    name=camel          Running

```

### Adding a Service

The entry point into the system is a Kubernetes Service as in [wildfly-camel-step03.json](../sources/wildfly-camel-step03.json)

To create a Service that accesses replicated Pods do

[TODO] use ref to master

```
$ kube apply -c https://raw.githubusercontent.com/wildfly-extras/wildfly-camel-book/2.1/sources/wildfly-camel-step03.json
I1203 14:28:44.860519 00001 kubecfg.go:613] Creation succeeded for Service with name rest-service
I1203 14:28:44.860770 00001 kubecfg.go:613] Creation succeeded for ReplicationController with name rest-controller
```

We now have a service

```
$ kube -l name=camel-srv list services
Name                Labels              Selector            IP                  Port
----------          ----------          ----------          ----------          ----------
rest-service        name=camel-srv      name=camel-pod      172.121.17.3        8081
```

that we can access like this

```
$ curl http://172.121.17.3:8081/example-camel-rest/rest/greet/hello/Kermit
Hello Kermit 
```

[TODO] remove publicIP limitation

> <small>We are waiting for publicIP support in Kubernetes to show up in OpenShift. Then we can access the REST endpoint and the admin console on a public IP</small>

## Domain Setup

Running multiple server containers in a cloud environment is often only useful when these containers can also be managed. In the previous example we had three servers that each exposed an HTTP service reachable through a Kubernetes [Service](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/services.md). The management interface of these servers were not exposed. It would be virtually impossible to adjust configurations for these individual servers or the whole set. As a minimum we would like to monitor the health state of these servers so that we can possibly replace containers if needed.

![](../images/example-domain-design.png)


### Starting the Domain

We can start the WildFly-CAmel domain like this
```
kube apply -c https://raw.githubusercontent.com/wildfly-extras/wildfly-camel-book/2.1/sources/wildfly-camel-domain.json
I1216 10:47:51.071633       1 kubecfg.go:613] Creation succeeded for Service with name management-service
I1216 10:47:51.071747       1 kubecfg.go:613] Creation succeeded for Service with name domain-controller
I1216 10:47:51.071758       1 kubecfg.go:613] Creation succeeded for Service with name rest-service
I1216 10:47:51.071761       1 kubecfg.go:613] Creation succeeded for ReplicationController with name master-replicator
I1216 10:47:51.071764       1 kubecfg.go:613] Creation succeeded for ReplicationController with name host-replicator

```

and verify the resulting servies like that
```
$ kube list services
Name                 Labels              Selector                                  IP                  Port
----------           ----------          ----------                                ----------          ----------
kubernetes                               component=apiserver,provider=kubernetes   172.121.17.213      443
kubernetes-ro                            component=apiserver,provider=kubernetes   172.121.17.239      80
management-service                       name=ctrl-pod                             172.121.17.49       9990
domain-controller                        name=ctrl-pod                             172.121.17.110      9999
rest-service                             name=http-pod                             172.121.17.60       8080
```

[TODO] remove publicIP limitation

> <small>Unfortunately, we can currently not use the public IP to access these services. I continue with private IPs</small>

We can now connect the WildFly command line interface like this

```
$ bin/jboss-cli.sh -c --controller=172.121.17.49:9990 --user=admin --password=admin
[domain@172.121.17.49:9990 /]
```

and deploy simple webapp for testing

```
deploy --runtime-name=endpoint.war --all-server-groups ~/git/wildfly-camel/itests/docker/domain/target/wildfly-camel-itests-docker-domain-2.1.0-SNAPSHOT.war
```


