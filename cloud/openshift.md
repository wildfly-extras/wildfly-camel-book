## OpenShift Origin

This chapter gets you started on WildFly-Camel in [OpenShift Origin](https://github.com/openshift/origin)  


### Starting OpenShift

We can start OpenShift Origin like this

```
$ docker run --rm --name openshift-origin --privileged --net=host \
  -v /:/rootfs:ro \
  -v /var/run:/var/run:rw \
  -v /sys:/sys:ro \
  -v /var/lib/docker:/var/lib/docker:rw \
  -v /var/lib/openshift/openshift.local.volumes:/var/lib/openshift/openshift.local.volumes \
  openshift/origin:v1.0.3 start
```

Then verify the OpenShift version

```
$ docker exec openshift-origin openshift version
openshift v1.0.3
kubernetes v1.0.0
```

You may want to create an alias to the OpenShift CLI

```
$ alias oc="docker exec openshift-origin oc"
```

## Standalone Servers

Here we run a set of WildFly Camel servers on [OpenShift Origin](https://github.com/openshift/origin). The target platform is Linux on an Amazon EC2 instance. When done, we have a set of portable [Docker](https://www.docker.io/) containers that can be deployed onto a production platform.

![](../images/example-rest-design.png)

The example architecture consists of a set of three high available (HA) servers running REST endpoints. For server replication and failover we use [Kubernetes](http://kubernetes.io). Each server runs in a dedicated [Pod](https://github.com/GoogleCloudPlatform/kubernetes/blob/v0.20.0/docs/pods.md) that we access via [Services](https://github.com/GoogleCloudPlatform/kubernetes/blob/v0.20.0/docs/services.md).

### Enable Images to Run with USER in the Dockerfile

WildFly Docker images run the application server under a custom user named 'jboss'.

To relax the security in your cluster so that images are not forced to run as a OpenShift pre-allocated UID, without granting everyone access to the privileged [security context constraints](https://docs.openshift.org/latest/admin_guide/manage_scc.html),
replace the 'restricted' security context.

Remove the existing security context

```
$ oc delete scc restricted
```

Now recreate it

```
$ oc create -f https://raw.githubusercontent.com/wildfly-extras/wildfly-camel-book/{{ book.version }}/sources/security-context-constriants.json
```

The `RunAsUser` strategy should now be `RunAsAny`

```
$ oc get scc restricted

NAME         PRIV      CAPS      HOSTDIR   SELINUX     RUNASUSER
restricted   false     []        false     MustRunAs   RunAsAny
```

Alternatively, if you don't want to do this, you'll have to create your own WildFly image which sets any files or directories written to by WildFly as world-writable.
See section **Support arbitrary user ids** in the [OpenShift image guidelines documentation](https://docs.openshift.org/latest/creating_images/guidelines.html). 


### Create an OpenShift project

This allows us to create pods, services and replication controllers under our own custom 'wildfly-camel' namespace instead of the 'default' namespace.

```
$ oc new-project wildfly-camel
```

Now allow the default `admin` user to access the project

```
$ oc policy add-role-to-user admin admin -n wildfly-camel
```

### Running a single Pod

A simple Pod configuration for a WildFly Camel container might be defined as in [wildfly-camel-step01.json](https://raw.githubusercontent.com/wildfly-extras/wildfly-camel-book/{{ book.version }}/sources/wildfly-camel-step01.json)

To create the Pod in OpenShift we do

```
$ oc create -f https://raw.githubusercontent.com/wildfly-extras/wildfly-camel-book/{{ book.version }}/sources/wildfly-camel-step01.json
```

You can see the running Pod like this

```
$ oc get pods -l name=camel

NAME                 READY     REASON    RESTARTS   AGE
wildfly-camel-rest   1/1       Running   0          15s
```

and delete it again with

```
$ oc delete pod -l name=camel
```

### Adding a ReplicationController

To achieve high availability (HA), lets replicate this Pod using a ReplicationController as in [wildfly-camel-step02.json](https://raw.githubusercontent.com/wildfly-extras/wildfly-camel-book/{{ book.version }}/sources/wildfly-camel-step02.json)

To create the replicated Pod in OpenShift we do

```
$ oc create -f https://raw.githubusercontent.com/wildfly-extras/wildfly-camel-book/{{ book.version }}/sources/wildfly-camel-step02.json
```

We now have three Pods each running an instance of our container

```
$ oc get pods

NAME                       READY     REASON    RESTARTS   AGE
wildfly-camel-rest-5fg43   1/1       Running   0          35s
wildfly-camel-rest-lqqc3   1/1       Running   0          35s
wildfly-camel-rest-ntjqz   1/1       Running   0          35s
```

### Adding a Service

The entry point into the system is a Kubernetes Service as in [wildfly-camel-step03.json](https://raw.githubusercontent.com/wildfly-extras/wildfly-camel-book/2.3/sources/wildfly-camel-step03.json)

To create a Service that accesses replicated Pods do

```
$ oc create -f https://raw.githubusercontent.com/wildfly-extras/wildfly-camel-book/2.3/sources/wildfly-camel-step03.json
```

We now have a service

```
$ oc get services -l name=camel-srv
NAME           LABELS           SELECTOR         IP(S)            PORT(S)
rest-service   name=camel-srv   name=camel-pod   172.30.103.178   8080/TCP
```

From a remote client, you should now be able to access the service like this

```
$ curl http://172.30.103.178:8080/example-camel-rest/rest/greet/hello/Kermit
Hello Kermit from 172.17.0.51
```

### OpenShift Console

You can see a diagramatic overview of replication controllers, services and pods from the OpenShift console. Use a web browser
to navigate to https://localhost:8443/console. Then log in using default username 'admin' with a password of 'admin'. Then click on
the wildfly-camel project and you should see a diagram like this.

![](../images/openshift-console-standalone.png)

## Domain Setup

Running multiple server containers in a cloud environment is often only useful when these containers can also be managed. In the previous example we had three servers that each exposed an HTTP service reachable through a Kubernetes [Service](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/services.md). The management interface of these servers were not exposed. It would be virtually impossible to adjust configurations for these individual servers or the whole set. As a minimum we would like to monitor the health state of these servers so that we can possibly replace containers if needed.

![](../images/example-domain-design.png)


### Starting the Domain

The WildFly-Camel domain can be configured as in [wildfly-camel-domain.json](https://raw.githubusercontent.com/wildfly-extras/wildfly-camel-book/2.3/sources/wildfly-camel-domain.json)

```
$ oc create -f https://raw.githubusercontent.com/wildfly-extras/wildfly-camel-book/{{ book.version }}/sources/wildfly-camel-domain.json
```

and verify the resulting servies like this
```
$ oc get services

NAME                 LABELS    SELECTOR        IP(S)            PORT(S)
domain-controller    <none>    name=ctrl-pod   172.30.108.179   9999/TCP
http-service         <none>    name=http-pod   172.30.35.216    8080/TCP
management-service   <none>    name=ctrl-pod   172.30.97.83     9990/TCP
```
Now, you should be able to access the admin console like this: http://172.30.97.83:9990/console

![](../images/console-domain.png)

The OpenShift console should display a diagram like this:

![](../images/openshift-console-domain.png)

We believe that managing deployments through the WildFly admin interface does not make much sense. Instead, deployments should already be backed into containers that you spin up in the various Pods.

There is a wide spectrum of opinion on whether this also applies to configuration. Here we retain the WildFly domain configurability (i.e. mutable containers for configuration).

Feedback is welcome.
