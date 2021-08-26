# Build and deploy a React app using Openshift

We will describe a simple pipeline from a repo to a working deployed app.
We'll cover here some basic principles as :

* Buildconfig strategies
* Deployment Config and Deployments
* Imagestreams
* Templates
* Jenkins and S2I
* Services and routes

1. To start our process to build and deploy the app we must create a project "devops" or any other name and to install jenkins in the created namespace. We can do that in different ways, using a chart, using operators, deploying from registry, but we'll use software catalog from openshift where it can be installed from a template provided by RedHat™. This can be done very easy in Developer view where you can add an app from catalog, in our case Jenkins ( persistent ). Wait for jenkins to be ready ant then proceed to the next step.

2. Next we'll need to bring the Jenkinsfile into the jenkins instance. This can be done in several ways: copying the pipeline into jenkins, using the svn, or the method we'll use - by applying a buildconfig for <b>Jenkinsfile</b>.  We can use cli tool <b>oc</b> or copy and paste the yaml into the wondow opened by pressing the plus sign in bottom right corner.


3. If you check the jenkins instance you'll see that the pipeline has been added and already started the build, but the binary build fails. This is because we need to import a second buildconfig which defines the s2i build method, in our case using a simple nginx s2i to inject the static html built from the react app hosted in this repo.

4. The builds stucks at binary build, and if we'll check the logs we can see that there is no stream for this. Because the service account that builds the image is limited, the image stream is not created, so we'll need to create our imagesream from the definition using one of the methods : cli or web ui.

5. Now if we run we'll se our build complete, and in the imagestream we'll find the latest label, as defined in the buildconfig for s2i. The next step is to deploy our application, and this can be achieved by applying the deploymentconfig from cli or ui. You can see the logs for the pod, and when the readyness probe will be ok the app will be up and running.

6. For you to access the app you need to create a route, but for you to create a route you need to create a service. The service makes your app discoverable to kubernetes, so you can access it from within kubernetes cluster, and the route connects to the service and exposes the application with an URL http(s)://[route].apps.[openshift.adress] .


> There you go ! we deployed an application using a jenkins build pipeline from scratch. [Show me the easy way](cherry.md)

## Command references
create a project
```bash
oc new-project devops
```
change namespace or project
```bash
oc project devops
```

apply a manifest
```bash
oc apply -f openshift\BC-jenkins.yaml
```

