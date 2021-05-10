Container = Your Code + Runtime

- A `Container` is a single process that is running isolated from the world around us and it may in turn have a series of drivers providing `process namespace`, `process isolation`, `file system isolation`, `resource isolation` in terms of maximum CPU and memory and its own network stack or `network isolation`.

- In Virtual Machine we carry around the OS which is quite heavy so it means all these virtual machines are carrying around every single component whereas in Containers we share the kernel with the base underlying OS, so 
there are some shared components there so in turn we actually get the benefit of not carrying around the OS.


In order to spin up a container we need to provide a container image, a file system and little bit of lightweight configuration.


Container Image  ---------> Container Runtime ----------> Running Container
    +
Configuration


We pass the configuration and container image to the container runtime. That container runtime is responsible for actually spinning everything behind the scenes. In turn you get a running container.

- `Running Container` is not a single component. It's a whole lot of different services in the background that are actually working together to provide all these different levels of isolation and abstraction.

- **Docker** provides you a toolkit to create all the container images, file systems. It then provides container runtime for you to actually pass the image into to get the services behind the scenes, spun up and it also provides then the long-term management being able to stop, monitor, get logs. You can perform everything docker can do using individual components and individual services.

- containers have names.


**CONTAINER IMAGE**:

- A `Container Image` is drastically different from a Virtual Machine Hard Drive or even a physical hard drive.
- It is made up of whole lot of layers.
- The container image itself is based on top of a previous image that in turn is based on a previous image that in turn is based on `Scratch`.

**Scratch** is the very bottom point. When we talk about Scratch it means nothing in the file system [blank hard drive].

> NOTE: We only bring along each layer as is needed and we only store the deltas.

One advantage is that if multiple layers uses same layer below it's share between the two in terms of being downloaded once stored once but being able to be referenced. So for these base layers we only need to keep one copy of them.


Running Images on Scratch (to be checked)


###### Making a Container Image:

Is there a need of a DockerFile to create a container image?
Ans: You don't need a dockerfile at all. It is not recommended for production however. But it's a great way to understand how a container image is made up.

`docker exec -it (tty) <container_id> command (bash)` -> In order to connect to container's process namespace and spawn our own process side by side. To a degree it allows us to run whatever inside the container itself.
Because you are now inside of container inside of process namespace with its attached file system and networking you can begin to interact with all of those components.

General guidance is never to do this on container is if we would have stopped it we will loose all of the changes.

- Changes to a Filesystem are completely temporary.
- The images themselves are mutable but we can take the changes we made and convert them into a container image.


`docker commit` -> The whole idea behind commit is we want to take changes we have made to a running container and pull it with the filesystem back into an image.

` docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]`


`RUN` is the equivalent of jumping into the container, executing that command and taking the snapshot of the resulting file system. So when you have **RUN** commands in a docker container DOCKERFILE it's the same as actuallyjumping in making those changes and then taking a snapshot.


If you have got a component that's not idempotent everytime you call it, it gives you a different result, everytime you build this docker container you are going to get completely different result.



Where do we keep our container images?
Ans: At the point of building they stay on our local machine and depending on things like Alpine, Nginx etc. they are stored in DockerHub and these are shorthand names for containers stored in different registries. So we canpull those container images down. We can push any custom containers up to our own private container registry so we can access them privately.

Private Container Registry are the images if it starts with a particular name.


**KUBERNETES**:

Headline Components of K8s:

1. Scheduling: Setting up containers across multiple different hosts.
2. Affinity/Anti-Affinity: Deciding where to actually put these containers.
3. Health Monitoring & Failover: deciding how to run those workloads in a reliable way.
4. Scaling: Being able to scale containers in and out.
5. Networking
6. Service Discovery
7. Coordinated App Upgrades

> In terms of running workloads (containers), Kubernetes is a `Scheduler`. You still need to contain a runtime on the mix to actually run your containers. 
- K8s doesn't actually run your containers. K8s talks to a container runtime, that container runtime runs your container. K8s is simply the scheduler that sits on the top that orchestrates those container runtimes to do
their thing. Docker is the default option and is one of the many options that are currently available.

You can use any container runtime under k8s with its pluggable architecture.

###### K8s Masters and Nodes:

Kubernetes is made up of **Master** and **Worker Nodes**.

Master (Node):

- This is what actually runs K8s.
- This is our control plane. This is where the brains behind K8s actually runs.
- This talks with individual Worker Nodes to execute workloads on those container runtimes and it's normally a cluster. You normally have a set of master nodes because you want it to be highly available.


Master contains three key components with regards to K8s:

1. API Server: This is what we talk to actually create and set up our configurations.
2. kube-scheduler: which decides which nodes these workloads actually sits on.
3. kube-controller-manager: which actually takes control of all the other components inside the ecosystem.


On top of these if you are running it yourself you'll also have a backing internal database. Most of the times it's `etcd` if you are running it internally. You can use external data stores of the configuration as well. And if you are running on a cloud you'll also have a cloud configuration manager which is what actually communicates with the underlying cloud provider itself. So if we are running custom containers on top of a cloud provider
you will probably have one of these.


Worker (Node):

- This is just some form of compute. These could be physical machines, VMs, Raspberry pie's etc.
- This runs your workloads and the container runtime lives here. This is what's actually kicking everything off.

In reality you have two core K8s services:

1. Kubelet: which actually talks to the container runtime to start and stop your containers.
2. kube-proxy: which is what sets up the networking layer.

You also have your container runtime of choice. By default this will be docker but you can switch it out if you prefer something slightly or different.


-- Master Node is where our configuration stores. This is the brains of the cluster.
-- Worker Nodes is where our workloads run. This is where everything happens.


###### Cloud Managed Masters: 

This means managing Master Nodes `as a Service`. There are a lot of Cloud Providers out there to manage master nodes for us i.e.

- Azure K8s Service
- Google Container Engine

These provides the management plan as a service. So you are no longer looking after those VMs. In return it's actually moving the master node management from being looking after VMs to simple clickbox configurations.


###### Kubernetes Nuts and Bolts:

When we look at K8s we don't tend to talk about explicit containers. We talk about `Pods`.

Pods:

- They are the lowest level deployable unit. This is what we spin up.
- A pod can be made up of one or more containers. In reality for majority of scenarios you will have only a single container in the pod.
- A pod to a degree is a single instance.


Your pod doesn't technically run. The containers run inside the pod and they will be allocated to a node but the pod itself never actually runs.

We can have multiple containers inside a pod and they can share network space so that they can talk backwards and forwards between each other and a whole lot of other components that you can set out on.


Where you use multiple containers inside a Pod?
Ans: If you want to do logging or monitoring etc.

Because of pod is the smallest unit we can deploy it's also the item that we scale.
- The lifecyle of all containers within a pod is stuck together. It's like gluing containers together and saying it's like an unit.

How to run K8s using Docker Desktop?
Ans: Docker Desktop actually contains Kubernetes as well.

Steps to enable K8s using Docker Desktop:

1. Click on Docker Desktop (make sure docker service/daemon is running).
2. Click on `Settings`.
3. Select Kubernetes Option on the left side and click `Enable Kubernetes` checkbox option.


This is great for Local Development purposes.

Alternative to this is `mini-kube` entirely contained k8s environments that you can use for development and testing purposes.


Context: This is the cluster that we are pointed to. Our context contains a pointer and some authentication details to a cluster or to that API server that sits on the master.

In order to administer and work with a K8s cluster we tend to use `kube-control`. kubectl talks to K8s API Server. This allows us to talk backwards and forwards.


`kubectl get pods (the object we like to get)` -> This allows us to see what pods are running within an environment.

What if we actually want to deploy a pod?
Ans: Don't do this using Command-Line. This has been deprecated. `Templates` are the way to go.


There are two ways to apply configurations:

1. file by file.
2. apply the entire directory in one go and be done with it.

Applying the entire directory is fantastic when you know order of things though you've only got one file in there.

`kubectl apply -f <yaml_file_path>` -> This allows you to apply a file.


What if the pod crashes and burns irrecoverably? What if the node it's on shuts down and stops?
Ans: A pod by itself doesn't give you any form of kubernetes resiliency to integrate. The container by itself depending on its restart policy may kick itself back off it that crashes but it won't by default move it across
nodes.

We have to think a level above pod to get any form of resiliency in the infrastructure.

The opposite of a `kubectl apply` is `kubectl delete` and it will use the names in the file itself to remove those components.


> Please don't use single naked pods. They're not designed to use that way.

A pod is great if you just need something to run but it doesn't gives you any layer of protection.


**ReplicaSet** [One level up from a Pod]:

- It is a way of defining a template for a pod and give it a count of how many we want.

> Note: The pods don't live inside the ReplicaSet. The ReplicaSet is responsible for spinning up those pods but they don't explicitly live inside. What the ReplicaSet does have though is a `Template` and that template allowsus to define what is deployed. 

If we say 2 replicas, the ReplicaSet will spin up those pods on our behalf for us.

-- There is no explicit relationship between a `ReplicaSet` and a `Pod`. We have to define one.

`kubectl get pods -o wide` -> This command will give an output as:

NAME                  READY   STATUS    RESTARTS   AGE    IP          NODE             NOMINATED NODE   READINESS GATES
demo3-replica-nw5t7   1/1     Running   1          112s   10.1.0.94   docker-desktop   <none>           <none>
demo3-replica-qvjf6   1/1     Running   2          4m     10.1.0.92   docker-desktop   <none>           <none>

In reality if we have a multiple network node cluster these will be spread across those.

> Note: We can change the Replica count, we can't change the Template. Technically you can but bad things happen.

ReplicaSet are great if we want to run one thing.

But what if we want to continuously update it?
- To do that we use `Deployments`.

**Deployment** [Level up the ReplicaSet]:

- A Deployment is responsible for creating multiple ReplicaSets as updates come in.
- Nice thing about it is we can actually rollback our changes.
- Deployment sits to the side of the ReplicaSet.
- A Deployment contains a Template and that Template is then deployed into the ReplicaSet. The ReplicaSet in turn then deploys the individual pods.
- If we change our container template (to v2 for example) the Deploymet spins up the brand new ReplicaSet and starts provisioning pods within there automatically. If it is successful, then it will reduce the number of 
replicas on our original ReplicaSet until it hits zero and until we have the correct number of replicas on our new target ReplicaSet. We can also failback using this method of actually undoing that change failing back to
that old ReplicaSet.

`kubectl get deployments` -> This allows us to see what deployments are running.


How can we talk to the workloads?
Ans: Using a `Service`.

- Service expose pods as a network service.
- They are simple DNS names.
- Uses an underlying `kube-proxy*`.

- A **Service** sits on top of Deployments and ReplicaSets and points directly to the pods themselves and it's to a degree of locator saying it wants to talk to this service which in turn talks to these pods.

We also have the ability to change how we publish our services. We can change

- `ClusterIP` which by default is internal only.
- `NodePort` if we want to make it available on every node as a port itself.
- `LoadBalancer` in cloud scenarios which basically means spin up a LB on my behalf and set it up.


###### Service and Publishing: 

`kubectl config get-contexts`

`kubectl config use-context <context_name>` -> This is used to switch from one cluster to another.

A **selector** is what pods should this service look towards. Without a selector your service won't point to anything.

`kubectl get services`

**INGRESS**:

- Exposing services is great and especially handy if you want multiple containers to talk to each other internally but sometimes you need a little bit more control or more importantly you want more control without taking on
responsibilities. This is where **Ingress** comes into play.

You can use Ingress Rules to define different paths that should be routed to containers. Under the hood it uses `ingress-controller`.

In order to talk to Ingress Rules we need to have deployed an ingress-controller.

`kubectl get services --namespace <namespace>` -> This allows us to switch to a different namespace.


###### Kubernetes Extras:

Autoscaling:

We can autoscale. We can scale our pods horizontally in and out based on metrics. We can scale our nodes our underlying compute in and out as well depending upon the provider meaning you can add completely elastic workloads.This does not come by default. You need to put it in your configuration. 

Kubernetes doesn't instantly mean auto-scaling, it just gives you the ability to set up auto-scaling.


Volumes:

These are stateful workloads. This has an ability to attach storage in such a way that you can run database ervice, stateful workloads whatever you want.


Namespaces:

These give us logical isolation on top of the cluster, so we can have one area for dev, one area for test, so forth and so on.


Affinity and anti-affinity:

We can control where pods are placed in terms of keeping them close to each other or as far away as possible.


Taints & Tolerations:

These allow us to define what pods should be run on what nodes with hard limits. example: You want to run a pod on a node that has a GPU attached that has some form of extra compute or speciality.


Resource Quotas:

We can also begin to limit how much memory the containers and pods can actually use.
