## Docker Integration

This chapter gets you started on WildFly-Camel on [Docker](https://www.docker.com/)  

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
127.0.0.1       localhost localhost.localdomain
172.30.0.233	ip-172-30-0-233
```

and make the docker daemon bind to a number of sockets

```
[ec2-user@ip-172-30-0-217 ~]$ cat /etc/sysconfig/docker
# Additional startup options for the Docker daemon
OPTIONS="-H tcp://127.0.0.1:2375 -H unix:///var/run/docker.sock"
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
