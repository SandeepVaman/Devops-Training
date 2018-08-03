# Working with labels
Let's talk about how we can search by labels. In order to do that, let me create some sample infrastructure for us to run.
```
$kubectl create -f sample-infrastructure-with-labels.yml 
pod/homepage-dev created
pod/homepage-staging created
pod/homepage-prod created
pod/login-dev created
pod/login-staging created
pod/login-prod created
pod/cart-dev created
pod/cart-staging created
pod/cart-prod created
pod/social-dev created
pod/social-staging created
pod/social-prod created
pod/catalog-dev created
pod/catalog-staging created
pod/catalog-prod created
pod/quote-dev created
pod/quote-staging created
pod/quote-prod created
pod/ordering-dev created
pod/ordering-staging created
pod/ordering-prod created
```
 All this is doing behind the scenes is creating a bunch of pods that have labels associated with them. So I'm going to clear screen, and do kubectl get pods. We have some pods and this example is a large e-commerce store that has different teams associated with different applications.
For example, there's a cart application, there's a catalog, there's a homepage and a login application, ordering, etc. And it also has different tiers, like dev, production, staging.
Let's take a look again to see if the pods are running. 
```
$kubectl get pods --show-labels
NAME               READY     STATUS    RESTARTS   AGE       LABELS
cart-dev           1/1       Running   0          16m       application_type=api,dev-lead=carisa,env=development,release-version=1.0,team=ecommerce
cart-prod          1/1       Running   0          16m       application_type=api,dev-lead=carisa,env=production,release-version=1.0,team=ecommerce
cart-staging       1/1       Running   0          16m       application_type=api,dev-lead=carisa,env=staging,release-version=1.0,team=ecommerce
catalog-dev        1/1       Running   0          16m       application_type=api,dev-lead=daniel,env=development,release-version=4.0,team=ecommerce
catalog-prod       1/1       Running   0          16m       application_type=api,dev-lead=daniel,env=production,release-version=4.0,team=ecommerce
catalog-staging    1/1       Running   0          16m       application_type=api,dev-lead=daniel,env=staging,release-version=4.0,team=ecommerce
homepage-dev       1/1       Running   0          16m       application_type=ui,dev-lead=karthik,env=development,release-version=12.0,team=web
homepage-prod      1/1       Running   0          16m       application_type=ui,dev-lead=karthik,env=production,release-version=12.0,team=web
homepage-staging   1/1       Running   0          16m       application_type=ui,dev-lead=karthik,env=staging,release-version=12.0,team=web
login-dev          1/1       Running   0          16m       application_type=api,dev-lead=jim,env=development,release-version=1.0,team=auth
login-prod         1/1       Running   0          16m       application_type=api,dev-lead=jim,env=production,release-version=1.0,team=auth
login-staging      1/1       Running   0          16m       application_type=api,dev-lead=jim,env=staging,release-version=1.0,team=auth
ordering-dev       1/1       Running   0          16m       application_type=backend,dev-lead=chen,env=development,release-version=2.0,team=purchasing
ordering-prod      1/1       Running   0          16m       application_type=backend,dev-lead=chen,env=production,release-version=2.0,team=purchasing
ordering-staging   1/1       Running   0          16m       application_type=backend,dev-lead=chen,env=staging,release-version=2.0,team=purchasing
quote-dev          1/1       Running   0          16m       application_type=api,dev-lead=amy,env=development,release-version=2.0,team=ecommerce
quote-prod         1/1       Running   0          16m       application_type=api,dev-lead=amy,env=production,release-version=1.0,team=ecommerce
quote-staging      1/1       Running   0          16m       application_type=api,dev-lead=amy,env=staging,release-version=2.0,team=ecommerce
social-dev         1/1       Running   0          16m       application_type=api,dev-lead=carisa,env=development,release-version=2.0,team=marketing
social-prod        1/1       Running   0          16m       application_type=api,dev-lead=marketing,env=production,release-version=1.0,team=marketing
social-staging     1/1       Running   0          16m       application_type=api,dev-lead=marketing,env=staging,release-version=1.0,team=marketing
```

It looks like all the pods are up and running. Next, let's take a look at the labels associated with them. Great. As you can see, we have a lot of labels associated with the application.

Once again, we notice that we have a lot of labels, and we've seen how we can list labels, but let's figure out how we can search for them. In order to do label searches, we want to use selectors with labels to do this. So clear my screen, and we are going to look for all of our labels that are in the production environment. In order to do this, I would do a kubectl get pods, just like before. I'm going to add a selector, and I'm going to put env=production.
```
$ kubectl get pods --selector env=production
NAME            READY     STATUS    RESTARTS   AGE
cart-prod       1/1       Running   0          19m
catalog-prod    1/1       Running   0          19m
homepage-prod   1/1       Running   0          19m
login-prod      1/1       Running   0          19m
ordering-prod   1/1       Running   0          19m
quote-prod      1/1       Running   0          19m
social-prod     1/1       Running   0          19m
```
And our list of pods has drastically reduced and all of the pods returned are pods that we have running in production. In fact, I'm going to do show-labels here, and as you can tell, we have a cart-prod, and we have an env in production. And if I search for this, you'll notice that all of my labels are associated with an application that's been tagged to run in a production-like setting.
```
$ kubectl get pods --selector env=production --show-labels
NAME            READY     STATUS    RESTARTS   AGE       LABELS
cart-prod       1/1       Running   0          20m       application_type=api,dev-lead=carisa,env=production,release-version=1.0,team=ecommerce
catalog-prod    1/1       Running   0          20m       application_type=api,dev-lead=daniel,env=production,release-version=4.0,team=ecommerce
homepage-prod   1/1       Running   0          20m       application_type=ui,dev-lead=karthik,env=production,release-version=12.0,team=web
login-prod      1/1       Running   0          20m       application_type=api,dev-lead=jim,env=production,release-version=1.0,team=auth
ordering-prod   1/1       Running   0          20m       application_type=backend,dev-lead=chen,env=production,release-version=2.0,team=purchasing
quote-prod      1/1       Running   0          20m       application_type=api,dev-lead=amy,env=production,release-version=1.0,team=ecommerce
social-prod     1/1       Running   0          20m       application_type=api,dev-lead=marketing,env=production,release-version=1.0,team=marketing
```
All right, let's do another example. Let's take a look at what we have. Let's search for somebody who's a dev-lead, 'cause a lot of times, different developers are associated with applications. So, kubectl get pods minus-minus selector and dev-lead, I'm going to search for Carisa.
```
$ kubectl get pods --selector dev-lead=carisa
NAME           READY     STATUS    RESTARTS   AGE
cart-dev       1/1       Running   1          23m
cart-prod      1/1       Running   1          23m
cart-staging   1/1       Running   1          23m
social-dev     1/1       Running   1          23m
```

So, in this scenario, we have four applications where we have a dev-lead and her name is Carisa. That's great. What if we wanted to do something more complicated where we want to add multiple labels and search by multiple labels? 
```
$ kubectl get pods --selector dev-lead=karthik,env=staging
NAME               READY     STATUS    RESTARTS   AGE
homepage-staging   1/1       Running   0          29m
```
So, we notice in this scenario out of all the applications that we had, we have one application called homepage-staging where we've tagged it with a dev-lead of Karthik, and it's running in a specific environment of staging. So this command, allows you to add multiple labels and search by multiple labels to filter down the pods that we have running.

In our example, we've talked about an application with a label, dev-lead=Karthik in an environment staging. What if we wanted to search for something that was not owned by Karthik? We can do this by adding the not flag.
```
$ kubectl get pods -l dev-lead!=karthik,env=staging
NAME               READY     STATUS    RESTARTS   AGE
cart-staging       1/1       Running   1          31m
catalog-staging    1/1       Running   1          31m
login-staging      1/1       Running   1          31m
ordering-staging   1/1       Running   1          31m
quote-staging      1/1       Running   1          31m
social-staging     1/1       Running   1          31m
```
The result of this is all of the applications that are not owned by Karthik in the staging environment. In fact, let's look at all the labels associated with it. 
```
$kubectl get pods -l dev-lead!=karthik,env=staging --show-labels
NAME               READY     STATUS    RESTARTS   AGE       LABELS
cart-staging       1/1       Running   2          33m       application_type=api,dev-lead=carisa,env=staging,release-version=1.0,team=ecommerce
catalog-staging    1/1       Running   1          33m       application_type=api,dev-lead=daniel,env=staging,release-version=4.0,team=ecommerce
login-staging      1/1       Running   1          33m       application_type=api,dev-lead=jim,env=staging,release-version=1.0,team=auth
ordering-staging   1/1       Running   1          33m       application_type=backend,dev-lead=chen,env=staging,release-version=2.0,team=purchasing
quote-staging      1/1       Running   1          33m       application_type=api,dev-lead=amy,env=staging,release-version=2.0,team=ecommerce
social-staging     1/1       Running   1          33m       application_type=api,dev-lead=marketing,env=staging,release-version=1.0,team=marketing
```

Next up, we want to actually learn how to use the in operator. 
```
$ kubectl get pods -l 'release-version in (1.0,2.0)' --show-labels
NAME               READY     STATUS    RESTARTS   AGE       LABELS
cart-dev           1/1       Running   2          40m       application_type=api,dev-lead=carisa,env=development,release-version=1.0,team=ecommerce
cart-prod          1/1       Running   2          40m       application_type=api,dev-lead=carisa,env=production,release-version=1.0,team=ecommerce
cart-staging       1/1       Running   2          40m       application_type=api,dev-lead=carisa,env=staging,release-version=1.0,team=ecommerce
login-dev          1/1       Running   2          40m       application_type=api,dev-lead=jim,env=development,release-version=1.0,team=auth
login-prod         1/1       Running   2          40m       application_type=api,dev-lead=jim,env=production,release-version=1.0,team=auth
login-staging      1/1       Running   2          40m       application_type=api,dev-lead=jim,env=staging,release-version=1.0,team=auth
ordering-dev       1/1       Running   2          40m       application_type=backend,dev-lead=chen,env=development,release-version=2.0,team=purchasing
ordering-prod      1/1       Running   2          40m       application_type=backend,dev-lead=chen,env=production,release-version=2.0,team=purchasing
ordering-staging   1/1       Running   2          40m       application_type=backend,dev-lead=chen,env=staging,release-version=2.0,team=purchasing
quote-dev          1/1       Running   2          40m       application_type=api,dev-lead=amy,env=development,release-version=2.0,team=ecommerce
quote-prod         1/1       Running   2          40m       application_type=api,dev-lead=amy,env=production,release-version=1.0,team=ecommerce
quote-staging      1/1       Running   2          40m       application_type=api,dev-lead=amy,env=staging,release-version=2.0,team=ecommerce
social-dev         1/1       Running   2          40m       application_type=api,dev-lead=carisa,env=development,release-version=2.0,team=marketing
social-prod        1/1       Running   2          40m       application_type=api,dev-lead=marketing,env=production,release-version=1.0,team=marketing
social-staging     1/1       Running   2          40m       application_type=api,dev-lead=marketing,env=staging,release-version=1.0,team=marketing
```
As we run that, we get a list of different applications, or pods, that are running that have a release-version between 1.0 and 2.0. 

What if we wanted to do something that's not in 1.0 or 2.0? Here we can use the notin operator, and I'm going to use show-labels as well.
```
$ kubectl get pods -l 'release-version notin (1.0,2.0)' --show-labels
NAME               READY     STATUS    RESTARTS   AGE       LABELS
catalog-dev        1/1       Running   2          41m       application_type=api,dev-lead=daniel,env=development,release-version=4.0,team=ecommerce
catalog-prod       1/1       Running   2          41m       application_type=api,dev-lead=daniel,env=production,release-version=4.0,team=ecommerce
catalog-staging    1/1       Running   2          41m       application_type=api,dev-lead=daniel,env=staging,release-version=4.0,team=ecommerce
homepage-dev       1/1       Running   0          41m       application_type=ui,dev-lead=karthik,env=development,release-version=12.0,team=web
homepage-prod      1/1       Running   0          41m       application_type=ui,dev-lead=karthik,env=production,release-version=12.0,team=web
homepage-staging   1/1       Running   0          41m       application_type=ui,dev-lead=karthik,env=staging,release-version=12.0,team=web 
```
So, here we see some applications, catalog-dev, for example, that's in a release-version of 4.0, 12.0 and so on and so forth. 

Sometimes we want to also be able to delete by labels. How do we go about doing that? So, we have pods... And we have a lot of them, and we might want to delete all of the pods associated with a specific dev-lead. 

Let's pick our favorite dev-lead, Karthik, and delete all the pods associated with him. In order to do this.
```
$ kubectl delete pods -l dev-lead=karthik
pod "homepage-dev" deleted
pod "homepage-prod" deleted
pod "homepage-staging" deleted
```
This goes ahead and deletes all of the pods that were associated with a specific dev-lead named Karthik. 
```
$ kubectl get pods --show-labels
NAME               READY     STATUS    RESTARTS   AGE       LABELS
cart-dev           1/1       Running   3          52m       application_type=api,dev-lead=carisa,env=development,release-version=1.0,team=ecommerce
cart-prod          1/1       Running   3          52m       application_type=api,dev-lead=carisa,env=production,release-version=1.0,team=ecommerce
cart-staging       1/1       Running   3          52m       application_type=api,dev-lead=carisa,env=staging,release-version=1.0,team=ecommerce
catalog-dev        1/1       Running   3          52m       application_type=api,dev-lead=daniel,env=development,release-version=4.0,team=ecommerce
catalog-prod       1/1       Running   3          52m       application_type=api,dev-lead=daniel,env=production,release-version=4.0,team=ecommerce
catalog-staging    1/1       Running   3          52m       application_type=api,dev-lead=daniel,env=staging,release-version=4.0,team=ecommerce
login-dev          1/1       Running   3          52m       application_type=api,dev-lead=jim,env=development,release-version=1.0,team=auth
login-prod         1/1       Running   3          52m       application_type=api,dev-lead=jim,env=production,release-version=1.0,team=auth
login-staging      1/1       Running   3          52m       application_type=api,dev-lead=jim,env=staging,release-version=1.0,team=auth
ordering-dev       1/1       Running   3          52m       application_type=backend,dev-lead=chen,env=development,release-version=2.0,team=purchasing
ordering-prod      1/1       Running   3          52m       application_type=backend,dev-lead=chen,env=production,release-version=2.0,team=purchasing
ordering-staging   1/1       Running   3          52m       application_type=backend,dev-lead=chen,env=staging,release-version=2.0,team=purchasing
quote-dev          1/1       Running   3          52m       application_type=api,dev-lead=amy,env=development,release-version=2.0,team=ecommerce
quote-prod         1/1       Running   3          52m       application_type=api,dev-lead=amy,env=production,release-version=1.0,team=ecommerce
quote-staging      1/1       Running   3          52m       application_type=api,dev-lead=amy,env=staging,release-version=2.0,team=ecommerce
social-dev         1/1       Running   3          52m       application_type=api,dev-lead=carisa,env=development,release-version=2.0,team=marketing
social-prod        1/1       Running   3          52m       application_type=api,dev-lead=marketing,env=production,release-version=1.0,team=marketing
social-staging     1/1       Running   3          52m       application_type=api,dev-lead=marketing,env=staging,release-version=1.0,team=marketing
```
We just have dev-leads of everybody except Karthik. So, in this scenario, we've deleted all of our pods where the dev-lead was Karthik. And there you have it. That's an example of how to use labels to search through your infrastructure. One of the nice things about labels, even though we don't have it in this specific example, is, we've used labels with pods in this example. However, you can also use labels with deployments, services, replica sets, et cetera.
So everything I've just shown you can also be transposed to those constructs for services and deployments.
