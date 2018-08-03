# Add, change and delete labels

We have two files to play around here helloworld-pod-with-labels.yml, and a sample-infrastructure-with-labels.yml. We're going to use both of these files as sample data that we want to create deployments and services with, which are already going to have labels associated with them. So for the first section, let's actually look at the helloworld-pod with yml.

Contents of helloworld-pod-with-labels.yml. 
```
apiVersion: v1
kind: Pod
metadata:
  name: helloworld
  labels:
    env: production
    author: karthequian
    application_type: ui
    release-version: "1.0"
spec:
  containers:
  - name: helloworld
    image: karthequian/helloworld:latest
Sandeeps-MacBook-Pro:04_01 sandeepvb$ 
```
This is a simple pod which has some labels called env production, author karthequian, application type ui, and a release version of 1.0.

Lets run this.
```
$ kubectl create -f helloworld-pod-with-labels.yml 
pod/helloworld created
```
Alright, sounds good. We have a helloworld pod, ready status, which is in the status of running and it's been online for seven seconds. So we've seen this before, but how do we actually look at the labels associated with that.
In order to do that, we would run kubectl get pods, just like before, and then we'd add the show labels tag.
```
$ kubectl get pods --show-labels
NAME         READY     STATUS    RESTARTS   AGE       LABELS
helloworld   1/1       Running   0          1m        application_type=ui,author=karthequian,env=production,release-version=1.0
```
As you can see, there's a new section called labels which have the application type ui, author, environment, and release version. Just as we had seen in our actual file. This is how we would add labels at deployment time. 

You can also add labels to the running pod because in some scenarios you might want to tag a pod after it's been deployed.
This is how we would do that. 
```
$ kubectl label po/helloworld app=helloworldapp --overwrite
pod/helloworld labeled

$ kubectl get pods --show-labels
NAME         READY     STATUS    RESTARTS   AGE       LABELS
helloworld   1/1       Running   0          5m        app=helloworldapp,application_type=ui,author=karthequian,env=production,release-version=1.0
```
There we see the real value of the new label. 
What happens in the scenarios where we want to delete a label? In order to do that we can actually rerun a kubectl label.
```
$ kubectl label po/helloworld app-
pod/helloworld labeled
$ kubectl get pods --show-labels
NAME         READY     STATUS    RESTARTS   AGE       LABELS
helloworld   1/1       Running   0          9m        application_type=ui,author=karthequian,env=production,release-version=1.0
```
You'll notice from before we had apphelloworld and we do not have that anymore. So that's how you actually end up deleting a label or overwriting it.
