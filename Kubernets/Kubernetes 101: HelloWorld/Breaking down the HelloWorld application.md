# Breaking down the HelloWorld application
To see what's actually deployed we can run a kubectl get all command. This returns everything that's actually deployed. 
```
$ kubectl get all
NAME                      READY     STATUS    RESTARTS   AGE
pod/hw-596b578c58-d6h7h   1/1       Running   0          19m

NAME                 TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
service/hw           NodePort    10.107.123.170   <none>        80:30873/TCP   9m
service/kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP        40m

NAME                 DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hw   1         1         1            1           19m

NAME                            DESIRED   CURRENT   READY     AGE
replicaset.apps/hw-596b578c58   1         1         1         19m
```
So we have the deployment, the result set, and we have the pods and services associated with the deployment, and finally we have the service hello world, or HW, on port 30873.
Let's take a look at our deployment that's up and running.
```
$ kubectl get deployments/hw
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
hw        1         1         1            1           22m
```
This is the command that we run before but if you do a -o yaml, this returns the yaml of the deployment.
```
$ kubectl get deployments/hw -o yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
  creationTimestamp: 2018-08-03T04:38:35Z
  generation: 1
  labels:
    run: hw
  name: hw
  namespace: default
  resourceVersion: "1784"
  selfLink: /apis/extensions/v1beta1/namespaces/default/deployments/hw
  uid: 15362d14-96d7-11e8-be1e-080027ab6bc4
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 2
  selector:
    matchLabels:
      run: hw
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        run: hw
    spec:
      containers:
      - image: karthequian/helloworld
        imagePullPolicy: Always
        name: hw
        ports:
        - containerPort: 80
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
  availableReplicas: 1
  conditions:
  - lastTransitionTime: 2018-08-03T04:39:14Z
    lastUpdateTime: 2018-08-03T04:39:14Z
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  - lastTransitionTime: 2018-08-03T04:38:35Z
    lastUpdateTime: 2018-08-03T04:39:14Z
    message: ReplicaSet "hw-596b578c58" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
  observedGeneration: 1
  readyReplicas: 1
  replicas: 1
  updatedReplicas: 1
```
Let's take a look at our service. 
```
$ kubectl get services
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
hw           NodePort    10.107.123.170   <none>        80:30873/TCP   18m
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP        48m

```
you'll notice that we have our hello world, or HW service running. Type node port and it's linked internally on port 80 but it's exposed on 30873. It's been online for 18 minutes. So let's open this up in the web browser.
```
minikube service hw
```
And this brings up our little application that we have if I reload this multiple times we'll notice that the little counter continues to keep incrementing.

One last thing to notice is that we deployed two applications. The deployment yaml and the service yaml. You can actually combine both of these things into a single yaml file. Lets take a look at helloworld-deployment.yml	and helloworld-service.yml.
helloworld-deployment.yml
```
#helloworld-deployment.yml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: helloworld-deployment
spec:
  selector:
    matchLabels:
      app: helloworld
  replicas: 1 # tells deployment to run 1 pods matching the template
  template: # create pods using pod definition in this template
    metadata:
      labels:
        app: helloworld
    spec:
      containers:
      - name: helloworld
        image: karthequian/helloworld:latest
        ports:
        - containerPort: 80
```
helloworld-service.yml
```
#helloworld-service.yml
apiVersion: v1
kind: Service
metadata:
  name: helloworld-service
spec:
  # if your cluster supports it, uncomment the following to automatically create
  # an external load-balanced IP for the frontend service.
  type: LoadBalancer
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: helloworld
```
However you can combine these two in helloworld-all.yml file.
helloworld-all.yml
```
#helloworld-all.yml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: helloworld-all-deployment
spec:
  selector:
    matchLabels:
      app: helloworld
  replicas: 1 # tells deployment to run 1 pods matching the template
  template: # create pods using pod definition in this template
    metadata:
      labels:
        app: helloworld
    spec:
      containers:
      - name: helloworld
        image: karthequian/helloworld:latest
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: helloworld-all-service
spec:
  # if your cluster supports it, uncomment the following to automatically create
  # an external load-balanced IP for the frontend service.
  type: LoadBalancer
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: helloworld
```

As we can see here, this is a file that has the both the deployment and the service portion of what we already deployed. We also have the three dashes here that signify a difference between the deployment and service sections. We call this a hello world all deployment and hello world all service. 

So lets run this file to create helloworld deployment.
```
$kubectl create -f helloworld-all.yml 
deployment.apps/helloworld-all-deployment created
service/helloworld-all-service created
```
This will create both our deployment and our service together. So if we do a kubectl get services and a kubectl get deployments we will notice that not only did we just deploy the service, we also deployed the deployment at the same time using one single file. This section is important because in a real world setting you typically have many services and many deployments that you want to deploy at once.
It's nicer to do that because you don't want to have multiple yaml files that comprise one single application. And with that we've broken down our hello world app.
