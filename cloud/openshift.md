## Introduction

In this guide we show how to run a set of WildFly Camel servers on [OpenShift V3](https://github.com/openshift/origin). The target platform is Linux on an Amazon EC2 instance. When done, we have a set of portable [Docker](https://www.docker.io/) containers that can be deployed onto a production platform.

![](../images/example-rest-design.png)

The example architecture consists of a set of three high available (HA) servers running REST endpoints. For server replication and failover we use [Kubernetes](http://kubernetes.io). Each server runs in a dedicated [Pod](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/pods.md) that we access via [Services](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/services.md).

### Installing Docker

SSH into your instance and type 

```
$ sudo yum install -y docker
$ sudo service docker start
``` 

to install and start Docker.

### Giving non-root access

The docker daemon always runs as the root user, and the docker daemon binds to a Unix socket instead of a TCP port. By default that Unix socket is owned by the user root, and so, by default, you can access it with sudo.

If you have a Unix group called `docker` and add users to it, then the docker daemon will make the ownership of the Unix socket read/writable by the `docker` group when the daemon starts. The docker daemon must always run as the root user, but if you run the docker client as a user in the `docker` group then you don't need to add sudo to all the client commands.

```
$ sudo usermod -a -G docker ec2-user
$ sudo service docker restart
``` 

> <small>You may have to logout/login for this change to take effect</small>

In my case I also had to add a host mapping like this

```
[ec2-user@ip-172-30-0-233 ~]$ sudo cat /etc/hosts
127.0.0.1   localhost localhost.localdomain
172.30.0.233	ip-172-30-0-233
```

### REST Endpoint Standalone

Part of the WildFly-Camel project is a [camel-rest](https://github.com/wildflyext/wildfly-camel/tree/master/examples/camel-rest) example that is also published as a [wildflyext/example-camel-rest](https://registry.hub.docker.com/u/wildflyext/example-camel-rest/) image.

You can run the standalone container like this

```
docker run --rm -ti --name camel-rest -p 8080:8080 wildflyext/example-camel-rest
```

and access the REST endpoint like this

```
$ curl http://localhost:8080/example-camel-rest/rest/greet/hello/Kermit
Hello Kermit
```

### Starting OpenShift Origin

Now we can start OpenShift Origin as a Docker container

```
$ docker run -rm -v /var/run/docker.sock:/var/run/docker.sock --net=host --privileged openshift/origin start
```

and verify the OpenShift version 

```
$ docker run -i openshift/origin version
openshift version 0.1, build 725d9ca
kubernetes v0.4-dev
```

We may also wnat to create an alias to the Kubernetes client

```
alias kube="docker run --rm --net=host -i openshift/origin kube"
```

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

As you can see, we now have three Pods each running an instance of our container

```
$ kube list pods
Name                                   Image(s)                        Host                Labels              Status
----------                             ----------                      ----------          ----------          ----------
13520066-7aef-11e4-9ea2-0624f808fac8   wildflyext/example-camel-rest   ip-172-30-0-233/    name=camel          Running
1351d659-7aef-11e4-9ea2-0624f808fac8   wildflyext/example-camel-rest   ip-172-30-0-233/    name=camel          Running
1352aa99-7aef-11e4-9ea2-0624f808fac8   wildflyext/example-camel-rest   ip-172-30-0-233/    name=camel          Running

```

### REST Endpoint Replicated

With OpenShift started we can now ask Kubernetes to create a Pod containing three example-camel-rest container instances. The Kubernetes JSON configuration can be found [here](http://git.io/xxWK2g).

```
KUBE="docker run --rm -i --net=host openshift/origin kube"
$KUBE apply -c http://git.io/xxWK2g
```

In order to test out the application, we need the IP address of the Pod containing our three WildFly-Camel servers. To get the IP run:

```
$KUBE list services
```

You should see some output on the console like the following:

```
Name                    Labels              Selector                                  IP                  Port
----------              ----------          ----------                                ----------          ----------
kubernetes                                  component=apiserver,provider=kubernetes   172.121.17.2        443
kubernetes-ro                               component=apiserver,provider=kubernetes   172.121.17.1        80
wildfly-camel-service                       name=wildfly-camel-rest-pod               172.121.17.3        8081
```

The last entry in the list named 'wildfly-camel-service' is our pod running the camel-rest demo. The IP address is 172.121.17.3 and the host port is 8081. With this information, you can open a browser and hit the Camel REST endpoint URL:

http://172.121.17.3:8081/example-camel-rest/rest/greet/hello/kermit

You should see a greeting displayed in the browser of 'Hello kermit'.