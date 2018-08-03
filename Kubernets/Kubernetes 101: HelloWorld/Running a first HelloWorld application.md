# Running a first HelloWorld application
In this section, we're going to actually run our first **hello world**, so we want to do a couple of things in this section. We want to start up Minikube for the first time, we want to set up our first Kubernetes helloworld application. We want to run the application in the Minikube environment and finally expose the application via load balancer and actually see it running. First off, let's start up Minikube. This can be accomplished by running below command.
```
minikube start
```
As you can tell, we are starting up a Kubernetes cluster and the first step is to download the Minikube ISO. One thing you want to note here is you want VirtualBox to be running behind the scenes. On the Mac VirtualBox behind scenes by default, however, maybe on Windows or Linux, you might want to start that. Once this command completes, we have a local Kubernetes environment set up for us via VirtualBox. If you want to take a look at that, type virtualbox and you'll notice that you have minikube that's up and running.
We can verify that Minikube and VirtualBox are talking to each other by doing a kubectl get nodes command.
```
$ kubectl get nodes
NAME       STATUS    ROLES     AGE       VERSION
minikube   Ready     master    40s       v1.10.0
```
This command returns a name, status, roles, age and version number. In this case we see the name is minikube and it's age of 40 seconds, which means we just created it. Also, we have version 1.10, which represents the Kubernetes version that we just installed. 

At this point in time we know we have minikube running and everything that we need to get the Kubernetes environment up and running is available. So the next step for us to do is actually run a **helloworld**. We'll run one of the most common hello world applications out there. To do this, we're going to type kubctl run. We're going to call this hw for our actual deployment. I'm going to pull an image called karthequian/helloworld and the image runs on port 80 so we're going to reference that.
```
$ kubectl run hw --image=karthequian/helloworld --port=80
deployment.apps/hw created
```
This command starts up a deployment calvled hw, so let's take a look. 
```
$kubectl get deployments
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
hw        1         1         1            1           1m
```
This returns our deployment with a desired state, current state, up to date and available of one, because we're running a single pod and it's been online for 1 minute.

We can also take a look at the replica sets.
```
$ kubectl get rs
NAME            DESIRED   CURRENT   READY     AGE
hw-596b578c58   1         1         1         3m
```
Our replica set is named similar to deployment with hw and then post fixed with a GUID. As we can see, it has a desired state, current state, ready state of one and has been online for 3 minutes. Next up, let's take a look at our pods. 
```
$ kubectl get pods
NAME                  READY     STATUS    RESTARTS   AGE
hw-596b578c58-d6h7h   1/1       Running   0          8m
```
And we have pod for our helloworld deployment that's been running for 8 minutes. The status of this is in a running state and we also see that the restarts of it have been zero.

So it looks like our deployment is online at this point in time. Next up we need to have a service for this deployment. The next thing we want to do is expose this as a service. 
``` 
$ kubectl expose deployment hw --type=NodePort 
service/hw exposed
```
So let's take a look at our service.
``` 
$ kubectl get services
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
hw           NodePort    10.107.123.170   <none>        80:30873/TCP   1m
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP        32m
```
As you can notice we have a service of name hw, it's of type NodePort and it's exposed on port 30873 and it's been online for 1 minute. 

To actually see this application up and running, there's one last thing we need to do.We need to tell Minikube to actually pull this up in the browser.
```
minikube service hw
```
This command will bring up your web browser on port 30873 and show our helloworld application up and running. As we can see, this is a Minikube IP, 192.168.99.102 running on port 30873 and this is our simple helloworld up and running.
So going back to our command prompt, we notice that our port is 30873, which matches the same port as we see on the web browser. And there you have it, that's your simple helloworld running in Kubernetes that you can also see on your web browser running via Minikube.
