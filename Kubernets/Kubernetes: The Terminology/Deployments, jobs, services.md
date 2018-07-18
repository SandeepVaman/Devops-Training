# Deployments, jobs, services
We've seen how pods are the basic building blocks in Kubernetes. As discussed in the previous section, we don't want to use them by themselves and want to use controllers instead.
In this section, we'll go over what controllers are and how they help us.Before we dive into the details, it's good to understand what problems controllers actually help us solve.

## Benifits of Controllers
1. **Application reliability**, where multiple instances of an application running prevent problems if one or more instance fails. 
2. **Scaling**, When your pods experience a high volume of requests, Kubernetes allows you to scale up your pods, allowing for a better user experience.
3. **Load balancing**, where having multiple versions of a pod running allow traffic to flow to different pods and doesn't overload one single pod or a node.

We'll cover the following kinds of controllers,
1. ReplicaSets
2. Deployments
3. DaemonSets
4. Jobs
5. Services

## ReplicaSets
<img src="images/What is ReplicaSet.png" alt="Replicasets">

> A ReplicaSet has one job, it ensures that the specified number of replicas for a pod are running at all times.
If the number of pods is less than what the ReplicaSet expects, for example, when a pod might have crashed, the ReplicaSet controller will start up a new pod, however, you can't actually declare a ReplicaSet by itself.
You'll need to use it within a deployment, so let's look at what that is.

## Deployment
<img src="images/What is Deployment?.png" alt="Deployment">

> A Deployment Controller provides declarative updates for pods and ReplicaSets.
This means that you can describe the desired state of a deployment in a YAML file and the Deployment Controller will align the actual state to match. 
Deployments can be defined to create new ReplicaSets or replace existing ones with new ones. Most applications are packages deployments, so chances are, you'll end up creating Deployments more than anything else.
Essentially, a deployment manages a ReplicaSet which, in turn, manages a pod.

<img src="images/Pod Replicaset Deployment.png" alt="Deployment, Replicaset, pod">
The benefit of this architecture is that deployments can automatically support a role-back mechanism. A new ReplicaSet is created each time a new Deployment config is deployed, but it also keeps the old ReplicaSet. This allows you to easily roll back to the old state if something isn't quite working correctly.

### Deployment Controller Use cases
Deployment Controllers and objects are higher-level constructs that were introduced to solve specific issues.
1. Pod management, where running a ReplicaSet allows us to deploy a number of pods and check their status as a single unit.
2. Scaling a ReplicaSet, which scales out the pods and allows for the deployment to handle more traffic. 
3. Pod updates and role-backs, where the Deployment Controller allows updates to the PodTemplateSpec. This creates a new 4. ReplicaSet and deploys a newer version of the pod. Also, if you don't like what you see in the newer version of the pod, just roll-back to the old ReplicaSet. 
4. Pause and resume. Sometimes we have larger change sets or multiple updates that need to happen to a deployment. In these scenarios, we can pause a deployment, make all the necessary updates, and then resume the deployment.Once the deployment is resumed, the new ReplicaSet will be started up and the deployment will update as expected. Please note that while a deployment is paused, it means that only updates are paused, but traffic will still get passed to the existing ReplicaSet as expected.
5. And finally, status. Getting the deployment status is an easy way to check for the health of your pods and identify issues during a roll-out.

## Replication Controller
You might have come across Replication Controllers if you search for Kubernetes controllers.This was an early implementation of ReplicaSets, but it has since been replaced by deployments and ReplicaSets, so in short, use deployments and ReplicaSets instead.
## DaemonSets
<img src="images/What are Daemonsets.png" alt="Demonsets">

> DaemonSets ensure that all nodes run a copy of a specific pod. As nodes are added or removed from the cluster, a DaemonSet will add or remove the required pods.
Deleting a DaemonSet will also clean up all the pods that it created. The typical use case for a DaemonSet is to run a single log aggregator or monitoring agent on a node.
## Job
<img src="images/What are Jobs.png" alt="Demonsets">

> As the name suggests, is basically a supervisor process for pods carrying out batch processes to completion. As the pod completes successfully, the job tracks information about the completion state of the pod.

Jobs are used to run individual processes that need to run once and complete successfully. Typically, jobs are run as a cron job to run a specific process at a specific time and repeat at another time. You might use a cron job to run a nightly report or database backups, for example.

## Services
<img src="images/What are services.png" alt="Services">

> A service provides network connectivity to one or more pods in your cluster.

When you create a service, it's designed a unique IP address that never changes through the lifetime of the service. Pods are then configured to talk to the service and can rely on the service IP on any requests that might be sent to the pod. Services are a really important concept because they allow one set of pods to communicate with another set of pods in an easy way. It's a best practice to use a service when you're trying to get two deployments to talk to each other.

That way, the pod in the first deployment always has an IP that they can communicate with regardless of whether the pod IPs in the second deployment changes. For example, when your front-end deployment needs to call a back-end deployment, you want to address the back-end with a service IP. Using the Backend Pod IP is a bad choice here because it can change over time and would wreak havoc for your application. A service provides an unchanging address so that the Frontend Pods can effectively talk to them at all times.

There's a few kind of services that you can use.

1. **Internal services**, where an IP is only reachable from within the cluster, this is the cluster IP in Kubernetes speak.
2. **External services**, where services running web servers, or publicly accessible pods, are exposed through an external endpoint. These endpoints are available on each node through a specific port. This is called a NodePort in Kubernetes speak. 
3. **Load balancer** This is for use cases when you want to expose your application to the public internet. It's only used when you are using Kubernetes in a cloud environment backed by a cloud provider such as AWS.
Lot of basic concepts have been covered in this section.Next up, we will learn about how to organize your applications with Labels, Selectors, and NameSpaces.
