# Kubernetes

[![GoDoc Widget]][GoDoc] [![CII Best Practices](https://bestpractices.coreinfrastructure.org/projects/569/badge)](https://bestpractices.coreinfrastructure.org/projects/569)

<img src="https://github.com/kubernetes/kubernetes/raw/master/logo/logo.png" width="100">

----

Kubernetes is an open source system for managing [containerized applications]
across multiple hosts; providing basic mechanisms for deployment, maintenance,
and scaling of applications.

Kubernetes builds upon a decade and a half of experience at Google running
production workloads at scale using a system called [Borg],
combined with best-of-breed ideas and practices from the community.

Kubernetes is hosted by the Cloud Native Computing Foundation ([CNCF]).
If you are a company that wants to help shape the evolution of
technologies that are container-packaged, dynamically-scheduled
and microservices-oriented, consider joining the CNCF.
For details about who's involved and how Kubernetes plays a role,
read the CNCF [announcement].

----


## Understanding Kubernetes
### Kubernetes (k8s) Notes

_Notes for Nigel Poulton's (@nigelpoulton) excellent [Getting Started With Kubernetes](https://app.pluralsight.com/library/courses/getting-started-kubernetes/) course. _

### Master (control plane)
Has 4 major components
* apiserver (kube-apiserver)
  * Entry point for all interactions with the Master,
  * Exposed on port :443
  * Users REST and JSON
  * commmands mostly sent using kubectrl

* Cluster storage
  * key-value storage
  * maintains state and config
  * uses etcd (persistent storage, distributed, consistent, watchable)
  * Very important and needs to be persisted to get the cluster working back again. 
  * Only stateful component in master

* controller (kube-controller-manager)
  * controller of controllers
    - Node Controller
    - Endpoint Controller
    - Namespace Controller
  * watches for state change
  * helps maintain desired state

* scheduler (kube-scheduler)
  * watches apiserver for new pods
  * assigns work to nodes
    * affinity/anti-affinity
    * constraints
    * resources

### Nodes (minions)
* kubelet
  * Main k8s agent and can be considered to be the node
  * registers node
  * instantiates pods (doesn't restarts)
  * watches apiserver
  * reports back to master
  * *inspect kubelets @ localhost:10255* 
  * exposes endpoints
    * specs
    * healthz
    * pods

* container runtime
  * does container management
    * pulling images
    * starting/stopping containers
  * Is pluggable but default is docker, but can also be coreos rocket

* kube-proxy
  * network brains
  * each pod shares one ip
  * use ports if you want to access individual pods
  * load balances across all pods behind the service

Every node or worker has it's own default pods such as logging, DNS, healthcheck etc.


### Pods
* Atomic unit of scheduling
* containers always run inside a pod.
* If there are multiple containers inside a pod, then those containers expose a port.
* *No naked containers* (ie, container without pod)
* sandboxed sort of
  * network stack
  * kernel namespace
  * ipc
  * mount
  * link etc
* all containers in a pod share pod env, shared
    * ip
    * network stack
    * Volumes
    * Talk to each other through pod's localhost
* tight coupling apps can be on a pod
* scaling is done by adding/removing pods
* If two containers in pod, the main one is called main container and the other is called sidecar container
* pod deployment is all or nothing, so not marked as up and running until everything is in place.
* Never brought to life. Lifecycle: born (pending) -> alive (running) -> dead (succeeded/failed)
* sometimes can be created by pod manifest file

#### Multi-container pod
* Tightly coupled, shared memory or volumes (ngix, node serving static/dynamic assets)

### Services
* services address issue with IP churn, new pods (while scaling or from replication controller) gets new IP 
* Sits in front of pods 
* provides stable IP and DNS name
* rolling updates adds the new pods to services and removes old ones
* pods belong to service via __labels__ (simple and powerful)
* __labels__ ties pods together through services and get load balanced
* Uses TCP by def  
* Sends traffic only to healthy pods
* can be configured for session affinity
* Can point to things outside of cluster
* Defaults to random load balancing (by DNS as well)
* Defaults to TCP

### Labels
* Simple and powerful feature
* Label ties things together
* Services laod balances based on labels.
* 

### Replication Controller
* Takes a pod and adds features to it.
* Essentiall does replication
* Legacy way of doing it
* Is being replaced by Deployments/Replica sets

### Deployments
* declarative
* self-documenting
* versioned
* spec onces, deploy many
* simple rolling updates/rollbacks
* REST objects
* First class citizens
* deployed via YAML/JSON manifest
* deployed via API server
* add features to replication controller (replica sets)
* Multiple concurrent versions
  * Blue-Green deployments
  * Canary releases

### To start using Kubernetes

See our documentation on [kubernetes.io].

Try our [interactive tutorial].

Take a free course on [Scalable Microservices with Kubernetes].

## To start developing Kubernetes

The [community repository] hosts all information about
building Kubernetes from source, how to contribute code
and documentation, who to contact about what, etc.

If you want to build Kubernetes right away there are two options:

##### You have a working [Go environment].

```
go get -d k8s.io/kubernetes
cd $GOPATH/src/k8s.io/kubernetes
make
```

##### You have a working [Docker environment].

```
git clone https://github.com/kubernetes/kubernetes
cd kubernetes
make quick-release
```

For the full story, head over to the [developer's documentation].

## Support

If you need support, start with the [troubleshooting guide],
and work your way through the process that we've outlined.

That said, if you have questions, reach out to us
[one way or another][communication].

[announcement]: https://cncf.io/news/announcement/2015/07/new-cloud-native-computing-foundation-drive-alignment-among-container
[Borg]: https://research.google.com/pubs/pub43438.html
[CNCF]: https://www.cncf.io/about
[communication]: https://git.k8s.io/community/communication
[community repository]: https://git.k8s.io/community
[containerized applications]: https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/
[developer's documentation]: https://git.k8s.io/community/contributors/devel#readme
[Docker environment]: https://docs.docker.com/engine
[Go environment]: https://golang.org/doc/install
[GoDoc]: https://godoc.org/k8s.io/kubernetes
[GoDoc Widget]: https://godoc.org/k8s.io/kubernetes?status.svg
[interactive tutorial]: https://kubernetes.io/docs/tutorials/kubernetes-basics
[kubernetes.io]: https://kubernetes.io
[Scalable Microservices with Kubernetes]: https://www.udacity.com/course/scalable-microservices-with-kubernetes--ud615
[troubleshooting guide]: https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/

[![Analytics](https://kubernetes-site.appspot.com/UA-36037335-10/GitHub/README.md?pixel)]()
