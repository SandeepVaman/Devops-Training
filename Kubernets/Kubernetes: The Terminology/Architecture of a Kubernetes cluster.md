# Architecture of a Kubernetes cluster
Let's take a look at the entire Kubernetes Cluster Architecture.

<img src="images/Architecture of Kubernetes.png" alt="image of Architecture of Kubernetes">

## Master Node
This guy is responsible for the overall management of the Kubernetes Cluster. Its got three components that take care of communication, scheduling, and controllers. These are the API Server, Scheduler, and Controller Manager. 

**a.Kube API Server** - as the name states, allows you to interact with the Kubernetes API. It's the front end of the Kubernetes control plane.

**b. Scheduler** - The Scheduler watches created Pods, who do not have a Node design yet, and designs the Pod to run on a specific Node. 

**c. Controller Manager** runs controllers. These are background threads that run tasks in a cluster. The controller actually has a bunch of different roles, all compiled into a single binary. The roles include, the Node Controller, who's responsible for the worker states, the Replication Controller, which is responsible for maintaining the correct number of Pods for the replicated controllers, the End-Point Controller, which joins services and Pods together.Service account and token controllers that handle access management. Finally, there's that CD, which is a simple distributed key value stored.

## etcd
Kubernetes uses it as its database and stores all cluster data here. Some of the information that might be stored, is job scheduling info, Pod details, stage information, etc. And, that's the Master Node.

## Kubectl
You interact with the Master Node using the this application, which is the command line interface for Kubernetes. Kubectl has a config file called a Kubeconfig. This file has server information, as well as authentication information to access the API Server. We wouldn't get anywhere without Worker Nodes, though. These Worker Nodes are the Nodes where your applications operate. The Worker Nodes communicate back with the Master Node.

## Kubelet
Communication to a Worker Node is handled by the Kubelet Process. It's an agent that communicates with the API Server to see if Pods have been designed to the Nodes. It executes Pod containers via the container engine.It mounts and runs Pod volume and secrets.
And finally, is aware of Pod of Node states and responds back to the Master. It's safe to say that if the Kubelet isn't working correctly on the Worker Node, you're going to have issues. Kubernetes is an container orchestrator, so the expectation is that you have a container native platform running on your Worker Nodes. This is where Docker comes in and works together with Kubelet to run containers on the Node.

## Kube-proxy
This process is the Network Proxy and load balancer for the service, on a single Worker Node. It handles the network routing for TCP and UDP Packets, and performs connection forwarding.

## Pod
Having the Docker Demon allows you to run containers. Containers of an application are tightly coupled together in a Pod. By definition, a Pod is the smallest unit that can be scheduled as a deployment in Kubernetes. This group of containers share storage, Linux name space, IP addresses, amongst other things.
They're also call located and share resources that are always scheduled together. Once Pods have been deployed, and are running, the Kubelet process communicates with the Pods to check on state and health, and the Kube-proxy routes any packets to the Pods from other resources that might be wanting to communicate with them. Worker Nodes can be exposed to the internet via load balancer. And, traffic coming into the Nodes is also handled by the Kube-proxy, which is how an End-user ends up talking to a Kubernetes application.
