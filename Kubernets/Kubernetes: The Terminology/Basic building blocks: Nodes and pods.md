# Basic building blocks: Nodes and pods
In this section, we'll take a deeper look at some of the components and concepts that one uses on worker Nodes to build applications and Kubernetes.

<img src="images/Kubernetes cluster.png" alt="Kubernetes cluster">
This is another way to look at the same architecture, but from a cluster perspective. From this perspective, the Master is responsible for managing the cluster. It coordinates all the activities in the cluster and communicates with the Nodes to keep Kubernetes and your applications running.

## Node
<img src="images/what is a node.png" alt="Definition of Node">

> The Node serves as a worker machine in the Kubernetes Cluster. One important thing to note is that this Node can be a physical computer or a virtual machine.

The Node has the following requirements.
1. It must have a Kubelet running,
2. It must have a container tooling, like Docker,
3. It must have a a kube-proxy process running, 
4. It must have a process like Supervisord, so it can restart components.

One thing to note is that if you're using Kubernetes in a production like setting, it's recommended that you have at least a three Node cluster.

For this course, we will use **Minikube**, which is a tool run Kubernetes locally. It's a lightweight Kubernetes implementation that creates a virtual machine, on your local box, and deploys a simple cluster containing of one single Node. Your applications run on Nodes, so let's take a look at the most basic construct needed to build a Kubernetes app.This is called a Pod.

## Pod
<img src="images/What is a pod.png" alt="Definition of Node">

> In the Kubernetes model, a Pod is the simplest unit that you can interact with. You can create, deploy, and delete Pods, and it represents one running process in your cluster.
A Pod contains the following things.
1. Your Docker application container
2. storage resources
3. a unique network IP
4. options that govern how the container should run.

In some scenarios, you can have multiple docker containers running in a Pod, but a Pod represents one single unit of deployment, a single instance of an application in Kubernetes that's tightly coupled and shares resources. 
Pods are designed to be ephemeral, disposable entities.
Pods also don't self-heal. If a Pod dies, for some reason, it will not be rescheduled. Also, if a Pod is exited from a Node because of lack of resources, it will not be restarted on different healthier Nodes. There are higher level constructs to manage and add stability to Pods, called **controllers**. So pro-tip, don't use a Pod directly. Use a controller instead, like a **deployment**.
Through its life-cycle, a Pod has the following states.

1. **Pending** - which means that the Pod has been accepted by the Kubernete system, but a container has not been created yet.
2. **Running** - where a Pod has been scheduled on a Node, and all of its containers are created, and at least one container is in a running state. 
3. **Succeeded** - which means that all the containers in the Pod have exited with an exit stat of zero, which indicates successful execution, and will not be restarted. 
4. **A failed state** - which means all the containers in the Pod have exited and at least one container has failed and returned a non-zero exit status.
5. **CrashLoopBackOff** - This is where a container fails to start, for some reason, and then Kubernetes tries over and over and over again to restart the Pod.

