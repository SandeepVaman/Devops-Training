# Application health checks
Let's figure out how to add application health checks to our existing helloworld application. Let's pull it up.
helloworld-with-probes.yaml
```
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: helloworld-deployment-with-probe
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
        readinessProbe:
          # length of time to wait for a pod to initialize
          # after pod startup, before applying health checking
          initialDelaySeconds: 10
          # Amount of time to wait before timing out
          initialDelaySeconds: 1
          # Probe for http
          httpGet:
            # Path to probe
            path: /
            # Port to probe
            port: 80
        livenessProbe:
          # length of time to wait for a pod to initialize
          # after pod startup, before applying health checking
          initialDelaySeconds: 10
          # Amount of time to wait before timing out
          timeoutSeconds: 1
          # Probe for http
          httpGet:
            # Path to probe
            path: /
            # Port to probe
            port: 80
```
We notice that the initial deployment of our helloworld application is still pretty much the same. We had the same container named helloworld pulling from the Karthequian helloworld image running on container port 80. Then we add two new probes to this, one called the readiness probe, and the second called the liveness probe. The readiness probe is something that Kubernetes uses to know when a container is actually ready to start accepting traffic.
It has an initial delay seconds, and the amount of time it should wait before it times out. In this example, because this is a web application, we'll use an HTP probe. So we're going to monitor an HTP get on the path of slash on port 80. The liveness probe is used by Kubernetes as a periodic check on the container to make sure that the container is still healthy. So in this case, it's going to wait for an initial 100 second delay, and it's going to look for a timeout of one second. Once again, we'll configure an HTP get on path slash on port 80.

So let's see this in action. This is going to create my deployment. 
```
$ kubectl create -f helloworld-with-probes.yaml 
deployment.apps/helloworld-deployment-with-probe created
$ kubectl get deployments
NAME                               DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
helloworld-deployment-with-probe   1         1         1            1           42s
$ kubectl get pods
NAME                                                READY     STATUS    RESTARTS   AGE
helloworld-deployment-with-probe-7f78698c95-8pnx5   1/1       Running   0          59s
```

This will also have pods associated with it which are in a running state as well. This is all working because we know it's been configured correctly, but what happens in scenarios when either a liveness probe fails or a readiness probe fails? In order to do that.
```
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: helloworld-deployment-with-bad-readiness-probe
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
        readinessProbe:
          # length of time to wait for a pod to initialize
          # after pod startup, before applying health checking
          initialDelaySeconds: 10
          # Amount of time to wait before timing out
          initialDelaySeconds: 1
          # Probe for http
          httpGet:
            # Path to probe
            path: /
            # Port to probe
            port: 90                     
```
Lets take a look at a file Let's take a look at the readiness probe first. So this is the same helloword application. Notice that it runs on port container port 80, however, we are going to probe port 90 which doesn't exist, and so Kubernetes is going to start up the application and then it's going to probe the slash on port 90 to see if the application is up and running.  
```
$ kubectl create -f helloworld-with-bad-readiness-probe.yaml 
deployment.apps/helloworld-deployment-with-bad-readiness-probe created
$ kubectl get deployments
NAME                                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
helloworld-deployment-with-bad-readiness-probe   1         1         1            0           13s
helloworld-deployment-with-probe                 1         1         1            1           7m
$ kubectl get pods
NAME                                                             READY     STATUS    RESTARTS   AGE
helloworld-deployment-with-bad-readiness-probe-9447b7b49-5wlmp   0/1       Running   0          2m
helloworld-deployment-with-probe-7f78698c95-8pnx5                1/1       Running   0          9m
```
It says that deployment has been created.
Let's take a look. Get deployments. We notice almost immediately that we have the deployment, but it's not available yet. Let's take a look at the pods. Get pods. Once again, we have a pod in a running status that's been up for 2 minutes, but it's returning a ready state of zero which means it's not ready, so we can actually describe the pod to see what's going on.
```
$ kubectl describe po/helloworld-deployment-with-bad-readiness-probe-9447b7b49-5wlmp
Name:           helloworld-deployment-with-bad-readiness-probe-9447b7b49-5wlmp
Namespace:      default
Node:           minikube/10.0.2.15
Start Time:     Fri, 03 Aug 2018 19:59:24 +0530
Labels:         app=helloworld
                pod-template-hash=500363605
Annotations:    <none>
Status:         Running
IP:             172.17.0.5
Controlled By:  ReplicaSet/helloworld-deployment-with-bad-readiness-probe-9447b7b49
Containers:
  helloworld:
    Container ID:   docker://e9dbb1857051d0155f7f4eec0ad801febf7f0874ed2a4aa7281f55a33c49b341
    Image:          karthequian/helloworld:latest
    Image ID:       docker-pullable://karthequian/helloworld@sha256:745af01b498d71c954dd3f11930918800de1bd89182b7415809a21d194872e88
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Fri, 03 Aug 2018 19:59:32 +0530
    Ready:          False
    Restart Count:  0
    Readiness:      http-get http://:90/ delay=1s timeout=1s period=10s #success=1 #failure=3
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-kxst2 (ro)
Conditions:
  Type           Status
  Initialized    True 
  Ready          False 
  PodScheduled   True 
Volumes:
  default-token-kxst2:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-kxst2
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type     Reason                 Age               From               Message
  ----     ------                 ----              ----               -------
  Normal   Scheduled              4m                default-scheduler  Successfully assigned helloworld-deployment-with-bad-readiness-probe-9447b7b49-5wlmp to minikube
  Normal   SuccessfulMountVolume  4m                kubelet, minikube  MountVolume.SetUp succeeded for volume "default-token-kxst2"
  Normal   Pulling                4m                kubelet, minikube  pulling image "karthequian/helloworld:latest"
  Normal   Pulled                 4m                kubelet, minikube  Successfully pulled image "karthequian/helloworld:latest"
  Normal   Created                4m                kubelet, minikube  Created container
  Normal   Started                4m                kubelet, minikube  Started container
  Warning  Unhealthy              1m (x20 over 4m)  kubelet, minikube  Readiness probe failed: Get http://172.17.0.5:90/: dial tcp 172.17.0.5:90: getsockopt: connection refused
```
Kubectl describe po/. The pod description gives us a lot of information, but the very last line in here that says, "warning, unhealthy," two seconds age, and it's done it 20 times over the last 4 minutes where we notice that the readiness probe has failed for 172.17.0.5 port 90, and the error is actually the connection was refused.
So in this case, we can tell that our pod while it's running is failing the readiness probe, and it's been checking it every 10 seconds, so we've had six failures over the last 52 seconds, and once we go back to get our pods once again, we still have this in a running status, but it's not ready, and if you do a kubectl get deployments, we'll notice that we still have no available deployments. So in this case, with a simulation of a bad readiness probe, we notice that the pod or the deployment aren't in a ready status.

We'll do a similar example for our liveness probe as well. 
```
$kubectl create -f helloworld-with-bad-liveness-probe.yaml 
deployment.apps/helloworld-deployment-with-bad-liveness-probe created
$ kubectl get deployments
NAME                                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
helloworld-deployment-with-bad-liveness-probe    1         1         1            1           28s
helloworld-deployment-with-bad-readiness-probe   1         1         1            0           8m
helloworld-deployment-with-probe                 1         1         1            1           15m
Sandeeps-MacBook-Pro:04_03 sandeepvb$ 
```
Let's take a look at what this is.
helloworld-with-bad-liveness-probe.yml. 
```
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: helloworld-deployment-with-bad-liveness-probe
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
        livenessProbe:
          # length of time to wait for a pod to initialize
          # after pod startup, before applying health checking
          initialDelaySeconds: 10
          # How often (in seconds) to perform the probe.
          periodSeconds: 5
          # Amount of time to wait before timing out
          timeoutSeconds: 1
          # Kubernetes will try failureThreshold times before giving up and restarting the Pod
          failureThreshold: 2
          # Probe for http
          httpGet:
            # Path to probe
            path: /
            # Port to probe
            port: 90

```
Similarl to what we saw earlier, this is a container running on port 80, the liveness probe checking on port 90, and we're going add a failure threshold of two where Kubernetes is going to try to restart the pod after it sees two failures, and it's going to check every five seconds, so by the time we go back chances are it would've tried to restart the pod.
```
$ kubectl create -f helloworld-with-bad-liveness-probe.yaml 
deployment.apps/helloworld-deployment-with-bad-liveness-probe created
$ kubectl get deployments
NAME                                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
helloworld-deployment-with-bad-liveness-probe    1         1         1            0           7m
helloworld-deployment-with-bad-readiness-probe   1         1         1            0           15m
helloworld-deployment-with-probe                 1         1         1            1           22m
$ kubectl get pods
NAME                                                             READY     STATUS             RESTARTS   AGE
helloworld-deployment-with-bad-liveness-probe-7fbddcfd4d-dpt7b   0/1       CrashLoopBackOff   7          9m
helloworld-deployment-with-bad-readiness-probe-9447b7b49-5wlmp   0/1       Running            0          17m
helloworld-deployment-with-probe-7f78698c95-8pnx5                1/1       Running            0          24m
```
Let's take a look at all our deployments first. Get deployments. We'll notice that we have our liveness probe, designed, current, up to date, and available for the last 7 minutes, but when we take a look at the pods, we'll see that we have the liveness probe helloworld-deployment-with-bad-liveness-probe. It's in a ready status, but there have been three restarts of this specific pod, and when we do a describe, get pod, I'm going to copy this, we notice that the pod is actually in a crash loop back off status 'cause it's tried to restart three times and from a Kubernetes perspective, it thinks that the pod is not actually up and running as expected, so that's the pod that ends up in a crash loop back off status.
