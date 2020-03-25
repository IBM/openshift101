# Exercise 1: Deploy a Node application with Source-to-Image

In this exercise, you'll deploy a simple Node.js Express application - "Example Health". Example Health is a simple UI for a patient health records system. We'll use this example to demonstrate key OpenShift features throughout this workshop. You can find the sample application GitHub repository here: [https://github.com/IBM/node-s2i-openshift](https://github.com/IBM/node-s2i-openshift)

## Deploy Example Health

Access your cluster on the [IBM Cloud clusters dashboard](https://cloud.ibm.com/kubernetes/clusters). Click the `OpenShift web console` button on the top-right. (This is a pop-up so you'll need to white list this site.)

Here is the main dashboard you should see.

![Main Dashboard](../.gitbook/assets/main-dashboard.png)

Create a project, on the left hand side, you can title it whatever you like, we suggest "example-health."

![Create Project](../.gitbook/assets/create-project.png)

And fill it out something like this.

![Example Health](../.gitbook/assets/example-health-create-project.png)

You should see a view that looks like this.

![New Project View](../.gitbook/assets/example-health-new-project.png)

Now click on "Administrator" and select "Developer.

![Developer](../.gitbook/assets/change-to-developer.png)

Click on the browse catalog button.

![Catalog](../.gitbook/assets/developer-catalog.png)

Scroll down to the `Node.js` image. Click on that catalog button.

![NodeJS](../.gitbook/assets/developer-nodejs.png)

Click `Create Application`.

You'll see an form like this:

![Create Source-to-Image Application](../.gitbook/assets/nodejs-gitrepo.png)

Enter the repository: `https://github.com/IBM/node-s2i-openshift`.

Then click the `Show Advanced Git Options`. and `/site` for the 'Context Dir'. Click 'Create' at the bottom of the window to build and deploy the application.

![Context Dir](../.gitbook/assets/nodejs-context.png)

Click on the center circle, then click "Start Build." You should see #1 Build start. You can click on the "View logs" to get more details.

![Build](../.gitbook/assets/nodejs-build.png)

When the build has deployed, find the "Routes." Click on that link:

![Successful Build](../.gitbook/assets/nodejs-success.png)

And you should see the login screen like the following:

![Login](../.gitbook/assets/nodejs-login.png)

You can enter any strings for username and password, for instance `test:test` because the app is running in demo mode.

Congrats! You've deployed a `Node.js` app to Kubernetes using OpenShift Source-to-Image (S2I).

## Understanding What Happened

[S2I](https://docs.openshift.com/container-platform/3.6/architecture/core_concepts/builds_and_image_streams.html#source-build) is a framework that creates container images from source code, then runs the assembled images as containers. It allows developers to build reproducible images easily, letting them spend time on what matters most, developing their code!

## Git Webhooks

So far we have been doing alot of manual deployment. In cloud-native world we want to move away from manual work and move toward automation. Wouldn't it be nice if our application rebuilt on git push events? Git webhooks are the way its done and openshift comes bundled in with git webhooks. Let's set it up for our project.

To be able to setup git webhooks, we need to have elevated permission to the project. We don't own the repo we have been using so far. But since its opensource we can easily fork it and make it our own.

Fork the repo at [https://github.com/IBM/node-s2i-openshift](https://github.com/IBM/node-s2i-openshift)

![Fork](../.gitbook/assets/fork.png)

Now that I have forked the repo under my repo I have full admin priviledges. As you can see I now have a settings button that I can change the repo settings with.

![Forked Repo](../.gitbook/assets/forked-repo.png)

We will come back to this page in a moment. Lets change our git source to our repo.

From our openshift dashboard for our project. Select `Builds`

![Build](../.gitbook/assets/nodejs-build-config.png)

Select the `node-s-2-i-openshift` build. As of now this should be the only build on screen.

![Select Build](../.gitbook/assets/nodejs-build-select.png)

Click on `Action` on the right and then select `Edit Build Config`

![Edit Build](../.gitbook/assets/nodejs-build-edit.png)

Change line `21` to `Git Repository URL` to our forked repository, and click `Save`.

![Save Build Config](../.gitbook/assets/nodejs-build-save.png)

You will see this will not result in a new build. If you want to start a manual build you can do so by clicking `Start Build`. We will skip this for now and move on to the webhook part.

Click on the main `Overview` tab.

Scroll down and click `Copy URL with Secret` for the GitHub Webook URL.

The webhook is in the structure

```text
https://c100-e.us-east.containers.cloud.ibm.com:31305/apis/build.openshift.io/v1/namespaces/example-health/buildconfigs/patientui/webhooks/<secret>/github
```

![Copy GitHub Webhook](../.gitbook/assets/github-url-secret.png)

> There is also the generic webhook url. This also works for github. But the github webhook captures some additional data from github and is more specific. But if we were using some other git repo like bitbucket or gitlab we would use the generic one.

In our github repo go to `Setting > Webhooks`. Then click `Add Webhook`

![Webhook Page](../.gitbook/assets/webhook-page.png)

In the Add Webhook page fill in the `Payload URL` with the url copied earlier from the build configuration. Change the `Content type` to `application/json`.

> **NOTE**: The *Secret* field can remain empty.

Right now just the push event is being sent which is fine for our use.

Click on `Add webhook`

![Add Webhook](../.gitbook/assets/add-webhook.png)

If the webhook is reachable by github you will see a green check mark.

Back in our openshift console we still would only see one build however. Because we added a webhook that sends us push events and we have no push event happening. Lets make one. The easiest way to do it is probably from the Github UI. Lets change some text in the login page.

Path to this file is `site/public/login.html` from the root of the directory. On Github you can edit any file by clicking the Pencil icon on the top right corner.

![Edit Page](../.gitbook/assets/edit-page.png)

Let's change the name our application to `Demo Health` (Line 21, Line 22). Feel free to make any other UI changes you feel like.

![Changes](../.gitbook/assets/changes.png)

Once done go to the bottom and click `commit changes`.

Go to the openshift build page again. This happens quite fast so you might not see the running state. But the moment we made that commit a build was kicked off.

![Running Build](../.gitbook/assets/nodejs-rebuild-webhook.png)

In a moment it will show completed. Navigate to the main overview page to find the route.

![Routes](../.gitbook/assets/nodejs-rebuild-overview.png)

> You could also go to `Applications > Routes` to find the route for the application.

If you go to your new route you will see your change.

![UI](../.gitbook/assets/nodejs-rebuild-updated.png)
