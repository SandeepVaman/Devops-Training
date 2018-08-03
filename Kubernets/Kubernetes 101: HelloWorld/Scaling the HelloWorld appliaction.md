# Scaling the HelloWorld appliaction
Let's figure out how we can scale our Hello World app, at this point.
```
$kubectl get deployments
NAME                        DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
helloworld-all-deployment   1         1         1            1           9m
hw                          1         1         1            1           51m
```
We notice that for all of our deployments right now, we have a single DESIRED ReplicaSet, a single CURRENT, single UP-TO-DATE, and single AVAILABLE. There are many scenarios where this is undesired. If for some reason the pod crashes, or ends up in some kind of crash loop, we will not have any instances of our application up and running. Which causes downtime until the pod can actually restart successfully.
Fortunately, we can use the in-built property of Kubernetes called ReplicaSets. So we can actually take a look at our ReplicaSets
```
$kubectl get rs 
NAME                                   DESIRED   CURRENT   READY     AGE
helloworld-all-deployment-7c46b4c7dc   1         1         1         11m
hw-596b578c58                          1         1         1         53m
```
And once again, we notice that they're all ones. So let's actually scale one of these deployments. So I'm going to pick the helloworld-deployment, and I'm going to scale this to have multiple pods as opposed to a single pod.
```
$ kubectl get deploy/helloworld-all-deployment
NAME                        DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
helloworld-all-deployment   3         3         3            3           16m
```
And we want to scale the replicas, so call replicas. And we want to scale it to three replicas, so we want to have three pods running for the single deployment, as opposed to one. 
```
$ kubectl scale --replicas=3 deploy/helloworld-all-deployment
deployment.extensions/helloworld-all-deployment scaled
```
Let's take a look at the deployment associated with helloworld-deployment now.
```
$ kubectl get deploy/helloworld-all-deployment
NAME                        DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
helloworld-all-deployment   3         3         3            3           16m
```
We'll notice now that we have three pods DESIRED. Three CURRENT, three UP-TO-DATE, and three AVAILABLE. And if you look at our pods, we'll notice that we had an initial pod that started 16 minutes ago. But now we also have two more pods associated with this.
So now what we have is, from the replica perspective, get rs. We notice that the helloworld-deployment has three, three, and three. And then looking at the pods, we have three pods for that single ReplicaSet. And there we have it. We have scaled our application, from a single pod, to three pods.
