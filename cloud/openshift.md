## OpenShift Integration

This chapter gets you started on WildFly-Camel in [OpenShift V3](https://github.com/openshift/origin)  

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
