# So what is OpenShift

To quote Wikipedia:

> OpenShift is a family of containerization software developed by Red Hat. Its flagship product is the OpenShift Container Platform-an on-premises platform as a service built around Docker containers orchestrated and managed by Kubernetes on a foundation of Red Hat Enterprise Linux.
> The Openshift UI has various functionalities, allowing one to monitor the container resources, container health, the nodes the containers reside on, IP addresses of the nodes, etc. The key store can be accessed via the Secrets in Openshift. The OC CLI command line tool also offers similar functionalities.

But the short of it? It's a abstraction layer **ON TOP** of Kubernetes. It's a way to empower Developers to deploy code and not worry about a lot of the underlying ecosystem. This workshop should show you the happy path to take advantage of most of the best parts of OpenShift and what it can offer.

The goals of this workshop are:

* To familiarize the reader with OpenShift
* Deploy a Node.js application to OpenShift
* Use OpenShift's features to monitor, scale the application

# Exercise 1: Deploy a Node application with Source-to-Image

In this exercise, you'll deploy a simple Node.js Express application - "Example Health". Example Health is a simple UI for a patient health records system. We'll use this example to demonstrate key OpenShift features throughout this workshop. You can find the sample application GitHub repository here: [https://github.com/IBM/node-s2i-openshift](https://github.com/IBM/node-s2i-openshift)

## Deploy Example Health

Access your cluster on the [IBM Cloud clusters dashboard](https://cloud.ibm.com/kubernetes/clusters). Click the `OpenShift web console` button on the top-right. (This is a pop-up so you'll need to white list this site.)

Here is the main dashboard you should see.

![Main Dashboard](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/main-dashboard.png)

Create a project, on the left hand side, you can title it whatever you like, we suggest "example-health."

![Create Project](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/create-project.png)

And fill it out something like this.

![Example Health](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/example-health-create-project.png)

You should see a view that looks like this.

![New Project View](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/example-health-new-project.png)

Now click on "Administrator" and select "Developer.

![Developer](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/change-to-developer.png)

Click on the browse catalog button.

![Catalog](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/developer-catalog.png)

Scroll down to the `Node.js` image. Click on that catalog button.

![NodeJS](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/developer-nodejs.png)

Click `Create Application`.

You'll see an form like this:

![Create Source-to-Image Application](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/nodejs-gitrepo.png)

Enter the repository: `https://github.com/IBM/node-s2i-openshift`.

Then click the `Show Advanced Git Options`. and `/site` for the 'Context Dir'. Click 'Create' at the bottom of the window to build and deploy the application.

![Context Dir](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/nodejs-context.png)

Click on the center circle, then click "Start Build." You should see #1 Build start. You can click on the "View logs" to get more details.

![Build](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/nodejs-build.png)

When the build has deployed, find the "Routes." Click on that link:

![Successful Build](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/nodejs-success.png)

And you should see the login screen like the following:

![Login](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/nodejs-login.png)

You can enter any strings for username and password, for instance `test:test` because the app is running in demo mode.

Congrats! You've deployed a `Node.js` app to Kubernetes using OpenShift Source-to-Image (S2I).

## Understanding What Happened

[S2I](https://docs.openshift.com/container-platform/3.6/architecture/core_concepts/builds_and_image_streams.html#source-build) is a framework that creates container images from source code, then runs the assembled images as containers. It allows developers to build reproducible images easily, letting them spend time on what matters most, developing their code!

## Git Webhooks

So far we have been doing alot of manual deployment. In cloud-native world we want to move away from manual work and move toward automation. Wouldn't it be nice if our application rebuilt on git push events? Git webhooks are the way its done and openshift comes bundled in with git webhooks. Let's set it up for our project.

To be able to setup git webhooks, we need to have elevated permission to the project. We don't own the repo we have been using so far. But since its opensource we can easily fork it and make it our own.

Fork the repo at [https://github.com/IBM/node-s2i-openshift](https://github.com/IBM/node-s2i-openshift)

![Fork](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/fork.png)

Now that I have forked the repo under my repo I have full admin priviledges. As you can see I now have a settings button that I can change the repo settings with.

![Forked Repo](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/forked-repo.png)

We will come back to this page in a moment. Lets change our git source to our repo.

From our openshift dashboard for our project. Select `Builds`

![Build](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/nodejs-build-config.png)

Select the `node-s-2-i-openshift` build. As of now this should be the only build on screen.

![Select Build](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/nodejs-build-select.png)

Click on `Action` on the right and then select `Edit Build Config`

![Edit Build](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/nodejs-build-edit.png)

Change line `21` to `Git Repository URL` to our forked repository, and click `Save`.

![Save Build Config](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/nodejs-build-save.png)

You will see this will not result in a new build. If you want to start a manual build you can do so by clicking `Start Build`. We will skip this for now and move on to the webhook part.

Click on the main `Overview` tab.

Scroll down and click `Copy URL with Secret` for the GitHub Webook URL.

The webhook is in the structure

```text
https://c100-e.us-east.containers.cloud.ibm.com:31305/apis/build.openshift.io/v1/namespaces/example-health/buildconfigs/patientui/webhooks/<secret>/github
```

![Copy GitHub Webhook](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/github-url-secret.png)

> There is also the generic webhook url. This also works for github. But the github webhook captures some additional data from github and is more specific. But if we were using some other git repo like bitbucket or gitlab we would use the generic one.

In our github repo go to `Setting > Webhooks`. Then click `Add Webhook`

![Webhook Page](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/webhook-page.png)

In the Add Webhook page fill in the `Payload URL` with the url copied earlier from the build configuration. Change the `Content type` to `application/json`.

> **NOTE**: The *Secret* field can remain empty.

Right now just the push event is being sent which is fine for our use.

Click on `Add webhook`

![Add Webhook](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/add-webhook.png)

If the webhook is reachable by github you will see a green check mark.

Back in our openshift console we still would only see one build however. Because we added a webhook that sends us push events and we have no push event happening. Lets make one. The easiest way to do it is probably from the Github UI. Lets change some text in the login page.

Path to this file is `site/public/login.html` from the root of the directory. On Github you can edit any file by clicking the Pencil icon on the top right corner.

![Edit Page](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/edit-page.png)

Let's change the name our application to `Demo Health` (Line 21, Line 22). Feel free to make any other UI changes you feel like.

![Changes](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/changes.png)

Once done go to the bottom and click `commit changes`.

Go to the openshift build page again. This happens quite fast so you might not see the running state. But the moment we made that commit a build was kicked off.

![Running Build](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/nodejs-rebuild-webhook.png)

In a moment it will show completed. Navigate to the main overview page to find the route.

![Routes](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/nodejs-rebuild-overview.png)

> You could also go to `Applications > Routes` to find the route for the application.

If you go to your new route you will see your change.

![UI](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/nodejs-rebuild-updated.png)


# Exercise 1: Deploy a Java application with a Docker Image

In this exercise, you'll deploy a simple Java microservice - "Authors" from a public image registry to OpenShift. You can find the image on Docker Hub here: [https://hub.docker.com/r/nheidloff/authors](https://hub.docker.com/r/nheidloff/authors)

## Deploy Authors application

Access your cluster on the [IBM Cloud clusters dashboard](https://cloud.ibm.com/kubernetes/clusters). Click the `OpenShift web console` button on the top-right. (This is a pop-up so you'll need to white list this site.)

Create a project, you can title it whatever you like, we suggest "example-authors."

![Create Project](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/createproject.png)

Click on your new project. You should see a view that looks like the image below. Click on the *Deploy Image* button on the main screen, or click on the *Add to Project* button and choose *Deploy Image* from there.

![Click 'Deploy Image'](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/assets_-LtBxDkdPh1ZKmLAzW5v_-LtiA8xoR9evM5RpWqWE_-LtiGF9_KJN2mKkBlNku_image.png)

At the pop up, choose the *Image Name* option. Enter `nheidloff/authors:v1` as the image name and click the search icon. Once the image metadata loads click the *Deploy* button.

![Add image name](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/assets_-LtBxDkdPh1ZKmLAzW5v_-LtiA8xoR9evM5RpWqWE_-LtiGIGDIs2-4z0I2z0H_image.png)

At the application overview page, click the *Create Route* option. This will give our application an external URL.

![Create a route](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/assets_-LtBxDkdPh1ZKmLAzW5v_-LtiA8xoR9evM5RpWqWE_-LtiGPdbdT1F2RjVG_eK_image.png)

The default options are sufficient, scroll down to the bottom and click the *Create* button.

![Click Create](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/assets_-LtBxDkdPh1ZKmLAzW5v_-LtiA8xoR9evM5RpWqWE_-LtiGSVIhmj0Kn3iuPoC_image.png)

Try to launch the application by copying the route's URL and appending `/openapi/ui`.

![Copy the URL](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/assets_-LtBxDkdPh1ZKmLAzW5v_-LtiA8xoR9evM5RpWqWE_-LtiGVIO2z7C4sIM2eDq_image.png)

{% hint style="info" %}
Add `/openapi/ui` to the URL!
{% endhint %}

The Open API user interface for our *Authors* service will load. We can test the application by clicking the **GET /v1/getauthor** API. Choose the *Try it out* button and entering "Niklas Heidloff" as the query.

![Open the Open API UI](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/assets_-LtBxDkdPh1ZKmLAzW5v_-LtiA8xoR9evM5RpWqWE_-LtiGZZBOVOQDdQ5b-96_image.png)

**Congratulations!** You just deployed an image from a public registry aviable from Docker Hub.

# Exercise 2: Logging and monitoring

In this exercise, we'll explore the out-of-the-box logging and monitoring capabilities that are offered in OpenShift.

## Simulate Load on the Application

First, let's simulate some load on our application. Run the following script which will endlessly spam our app with requests:

> With Linux/MacOS

```bash
while sleep 1; do curl -s <your_app_route>/info; done
```

> With Windows

```bash
while($true){curl <your_app_route>/info}
```

{% hint style="info" %}
Note: Retrieve the external URL from the OpenShift console, or from the URL of your Example Health application. Note that there may be an `/index.html` at the end that you need to replace with `/info`. We're hitting the /info endpoint which will trigger some logs from our app. For example:

[`http://patientui-health-example.myopenshift-xxx.us-east.containers.appdomain.cloud/info`](http://patientui-health-example.myopenshift-341665-66631af3eb2bd8030c5bb56d415b8851-0001.us-east.containers.appdomain.cloud/jee.html)
{% endhint %}

## OpenShift Logging

Since we only created one pod, seeing our logs will be straight forward. Navigate to `View Logs` on the left on the main dashboard.

![Pods](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/view-logs.png)

You should be taken to something like the following. Scroll up and you should see the `DEBUG` like in the image. Scroll back down, and you should see a new line every second per the `curl` above.

![Logs](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/view-logs-details.png)

## OpenShift Terminal

One of the great things about Kubernetes is the ability to quickly debug your application pods with SSH terminals. This is great for development, but generally is not recommended in production environments. OpenShift makes it even easier by allowing you to launch a terminal directly in the dashboard.

Switch to the `Terminal` tab, and run the following commands.

```bash
# This command shows you the the project files.
ls
```

```bash
# This command shows you the running processes.
ps aux
```

![Terminal](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/terminal-output.png)

## OpenShift Monitoring

When deploying new apps, making configuration changes, or simply inspecting the state of your cluster, the OpenShift monitoring dashboard gives you an overview of your running assets.

You can also dive in a bit deeper - the `Events` tab is very useful for identifying the timeline of events and finding potential error messages.

![View Details](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/event-details.png)

You'll want to refer to this view throughout the lab. Almost all actions we take in in OpenShift will result in an event being fired in this view. As it is updated real-time, it's a great way to track changes to state.

# Exercise 3: Metrics and dashboards

In this exercise, we'll explore the third-party monitoring and metrics dashboards that are installed for free with OpenShift!

## Grafana

Red Hat OpenShift on IBM Cloud comes with [Grafana](https://grafana.com/) preinstalled. Get started by switching to the Administrator view:

![Administrator](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/switch-to-admin.png)

Then Navigate to `Monitoring > Dashboards` in the left-hand bar. You'll be asked to login with OpenShift and then click through some permissions.

![Monitoring Dashboards](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/dashboard-menu.png)

This will open up another proxy page, click `Log in with OpenShift`.

Next, it will ask you for `Authorize Access`, take the default which is both checkboxes, and click `Allow selected permissions`.

You should then see your Grafana dashboard. Hit `Home` on the top left, and choose `K8s / Compute Resources / Namespace (Pods)`.

![Grafana](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/grafana-namespace.png)

Choose the name of the project you created in [Step 1](exercise-2.md#deploy-example-health) - the same one that your application is running inside.

You should be able to see the CPU and Memory usage for your application. In production environments, this is helpful for identifying the average amount of CPU or Memory your application uses, especially as it can fluctuate through the day. We'll use this information in the next exercise to set up auto-scaling for our pods.

![Grafana also project](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/grafana-example-health.png)

## Prometheus and Alert Manager

Navigating back to the cluster console, you can also launch:

* **[Prometheus](https://prometheus.io/)** - a monitoring system with an efficient time series database
* **[Alertmanager](https://prometheus.io/docs/alerting/alertmanager/)** - an extension of Prometheus focused on managing alerts

![Metrics, Alerts and Dashboards](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/monitoring-dashboard4.png)

## Prometheus

![Prometheus](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/prometheus-time-series4.png)

## Alertmanager

![Alert Manager](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/alert-manager4.png)

# Exercise 4: Scaling the application

In this exercise, we'll leverage the metrics we've observed in the previous step to automatically scale our UI application in response to load.

## Enable Resource Limits

Before we can setup autoscaling for our pods, we first need to set resource limits on the pods running in our cluster. Limits allows you to choose the minimum and maximum CPU and memory usage for a pod.

Hopefully you have your running script simulating load \(if not go [here](exercise-2.md#simulate-load-on-the-application)\), Grafana showed you that your application was consuming anywhere between ".002" to ".02" cores. This translates to 2-20 "millicores".
That seems like a good range for our CPU request, but to be safe, let's bump the higher-end up to 30 millicores. In addition, Grafana showed that the app consumes about `25`-`35` MB of RAM. Set the following resource limits for your deployment now.

Switch to the **Administrator** view and then navigate to **Workloads > Deployments** in the left-hand bar. Choose the `patient-ui` Deployment, then choose **Actions > Edit Deployment**.

![Deployments](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/ocp-deployments.png)

In the YAML editor, go to line 44. In the section **template > spec > containers**, add the following resource limits into the empty resources. Replace the `resources {}`, and ensure the spacing is correct -- YAML uses strict indentation.

![Limits YAML](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/ocp-limits-yaml.png)

  ```yaml
             resources:
               limits:
                 cpu: 30m
                 memory: 100Mi
               requests:
                 cpu: 3m
                 memory: 40Mi
  ```

**Save** and **Reload** to see the new version.

Verify that the replication controller has been changed by navigating to **Events**

![Resource Limits](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/ocp-dc-events.png)

## Enable Autoscaler

Now that we have resource limits, let's enable autoscaler.

By default, the autoscaler allows you to scale based on CPU or Memory. The UI allows you to do CPU only \(for now\). Pods are balanced between the minifmum and maximum number of pods that you specify. With the autoscaler, pods are automatically created or deleted to ensure that the average CPU usage of the pods is below the CPU request target as defined.
In general, you probably want to start scaling up when you get near `50`-`90`% of the CPU usage of a pod. In our case, let's make it `1`% to test the autoscaler since we are generating minimal load.

1. Navigate to **Workloads > Horizontal Pod Autoscalers**, then hit **Create Horizontal Pod Autoscaler**.

![HPA](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/ocp-hpa.png)

```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: patient-hpa
  namespace: example-health
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: patient-ui
  minReplicas: 1
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        targetAverageUtilization: 1
```

Hit **Create**.

## Test Autoscaler

If you're not running the script from the [previous exercise](exercise-2.md#simulate-load-on-the-application), the number of pods should stay at 1.

Check by going to the **Overview** page of **Deployments**.

![Scaled to 1 pod](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/ocp-hpa-before.png)

Start simulating load by hitting the page several times, or running the script. You'll see that it starts to scale up:

![Scaled to 4/10 pods](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/ocp-hpa-after.png)

That's it! You now have a highly available and automatically scaled front-end Node.js application. OpenShift is automatically scaling your application pods since the CPU usage of the pods greatly exceeded `1`% of the resource limit, `30` millicores.

### Optional

If you're interested in setting up the CLI, [follow the steps here](../getting-started/setup_cli.md). Then, run the following command in your CLI `oc get hpa` to get information about your horizontal pod autoscaler. Remember to switch to your project first with `oc project <project-name>`.

You could have created the autoscaler with the command `oc autoscale deployment/patient-ui --min 1 --max 10 --cpu-percent=1`.

# Exercise 5: Health checks

In Kubernetes, liveness and readiness probes are essential for smoothly running applications.
A probe is generally a REST `GET` call, but there are other types of probes available.
Liveness probes are used to determine when to restart a container. For example, an
application that is unhealthy and no longer responding to an API call would be
restarted by OpenShift. Readiness probes determine when a container is ready to
start receiving traffic. If a readiness probe fails, then the load balancer
would deregister that service.

## Create Readiness and Liveness Probes

The `/info` endpoint on the Example Health application is a great way to check
whether the application is running and responding to API calls -- it responds
with a simple JSON payload.

Go ahead and click on the deployment view. You should see something like `node-s-2-i-openshift`. Click `Actions > Edit Deployment`. Find the `containers` line probably line _38_. Under the `resources` line, so line **46** past the following `yaml`. This will add a liveness probe to your deployment!

```yaml
    livenessProbe:
      initialDelaySeconds: 5
      periodSeconds: 2
      httpGet:
        path: /info
        port: 8080
```

Now, next step is to add a readiness probe. Luckly it's on the same page, imediatly under the `livenessProbe` stanza you entered, paste the following:

```yaml
    readinessProbe:
    initialDelaySeconds: 5
    timeoutSeconds: 2
    httpGet:
      path: /info
      port: 8080
```

This will make sure when a new deployment happens that it won't start until the `/info` path is available.

If you want to verify it, you can run the following command to check the status, you should see something like this in the output:

```bash
oc describe deployment node-s-2-i-openshift
```

```console
Liveness:     http-get http://:8080/info delay=5s timeout=1s period=2s #success=1 #failure=3
Readiness:    http-get http://:8080/info delay=5s timeout=2s period=10s #success=1 #failure=3
```

If all works, everything should be the same. Let's check that the probes are really working though.

## Inject Failure

Let's edit the probe with a typo to see what happens when it fails. Edit the health check and change the path for the readiness probe to `/badpath`. Wait a few minutes and check your deployment - you'll notice that `0/1` containers are ready:

![Badpath](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/badpath.png)

Dive into your events and you'll see that the probe is failing, causing the platform to try and repeatedly restart your pod.

![Deeper Dive](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/events.png)

Using health checks gives your OpenShift service layer better reliability and helps you start with a strong foundation.

# Exercise 6: Deploy a Node application with Build Config (CLI version)

In this exercise we'll revisit the application from exercise 1, except we'll use equivalent CLI commands to deploy our "Example Health" application.

From the IBM Cloud console launch the IBM Cloud Shell. Refer to our [Getting Starting](../pre-work/CLOUD_SHELL.md) material to learn how to access the IBM Cloud Shell.

## Deploy Example Health (CLI version)

First, clone the *Example Health* source code and change to that directory.

```bash
git clone https://github.com/IBM/node-build-config-openshift
cd node-build-config-openshift
```

Take note of the new `Dockerfile` in the application's root directory. We've pre-written it for you. But we've copied it here too, go through each line and read the corresponding comment.

```Dockerfile
# Use the official Node 10 image
FROM node:10

# Change directory to /usr/src/app
WORKDIR /usr/src/app

# Copy the application source code
COPY . .

# Change directory to site/
WORKDIR site/

# Install dependencies
RUN npm install

# Allow traffic on port 8080
EXPOSE 8080

# Start the application
CMD [ "npm", "start" ]
```

From the OpenShift console click the user name in the top right corner and select *Copy Login Command*.

The login command will be copied to the clipboard, in the IBM Cloud Shell, paste that command. For example:

```bash
oc login https://c100-e.us-south.containers.cloud.ibm.com:30403 --token=jWX7a04tRgpdhW_iofWuHqb_Ygp8fFsUkRjOK7_QyFQ
```

Create a new OpenShift project to deploy our application, call it `example-health-ns`.

```bash
oc new-project example-health-ns
```

Build your application's image by running the `oc new-build` command from your source code root directory. This will create a Build and an ImageStream of the app.

```bash
oc new-build --strategy docker --binary --docker-image node:10 --name example-health
```

The output should look like below:

```bash
oc new-build --strategy docker --binary --docker-image node:10 --name example-health
--> Found Docker image aa64327 (3 weeks old) from Docker Hub for "node:10"

    * An image stream tag will be created as "node:10" that will track the source image
    * A Docker build using binary input will be created
      * The resulting image will be pushed to image stream tag "example-health:latest"
      * A binary build was created, use 'start-build --from-dir' to trigger a new build

--> Creating resources with label build=example-health ...
    imagestream.image.openshift.io "node" created
    imagestream.image.openshift.io "example-health" created
    buildconfig.build.openshift.io "example-health" created
--> Success
```

Start a new build using the `oc start-build` command.

```bash
oc start-build example-health --from-dir . --follow
```

The output should look like below:

```bash
oc start-build example-health --from-dir . --follow
Uploading directory "." as binary input for the build ...
.
Uploading finished
build.build.openshift.io/example-health-1 started
Receiving source from STDIN as archive ...
Replaced Dockerfile FROM image node:10
...
Successfully built 11bff161eb8e

Pushing image docker-registry.default.svc:5000/example-health-ns/example-health:latest ...
Pushed 0/12 layers, 17% complete
Pushed 1/12 layers, 42% complete
...
Pushed 11/12 layers, 100% complete
Pushed 12/12 layers, 100% complete
```

Finally, deploy the application by running `oc new-app`.

```bash
oc new-app -i example-health
```

The output should look like below:

```bash
$ oc new-app -i example-health
--> Found image 11bff16 (8 minutes old) in image stream "example-health-ns/example-health" under tag "latest" for "example-health"

    * This image will be deployed in deployment config "example-health"
    * Port 8080/tcp will be load balanced by service "example-health"
      * Other containers can access this service through the hostname "example-health"
    * WARNING: Image "example-health-ns/example-health:latest" runs as the 'root' user which may not be permitted by your cluster administrator

--> Creating resources ...
    deploymentconfig.apps.openshift.io "example-health" created
    service "example-health" created
--> Success
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/example-health'
    Run 'oc status' to view your app.
```

Expose the service using `oc expose`, a route will be created.

```bash
oc expose svc/example-health
```

Find the application's route by running `oc get routes`.

```bash
oc get routes
```

The output should look like below:

```bash
$ oc get routes

NAME             HOST/PORT                                                                                                                        PATH      SERVICES         PORT       TERMINATION   WILDCARD
example-health   example-health-example-health-ns.aida-dev-apps-10-30-f2c6cdc6801be85fd188b09d006f13e3-0001.us-south.containers.appdomain.cloud             example-health   8080-tcp                 None
```

Copy the URL into a browser and log into the site with `admin`:`test`.

![Example Health details](https://raw.githubusercontent.com/IBM/openshift101/skills-network/workshop/.gitbook/assets/example-health-app.png)

**Congratulations** on completing this exercise!
