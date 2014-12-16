## OpenShift Integration

This chapter gets you started on WildFly-Camel in [OpenShift V3](https://github.com/openshift/origin)  


### Starting OpenShift Origin

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
