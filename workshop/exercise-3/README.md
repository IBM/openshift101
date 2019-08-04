# Exercise 3 - Deploying a New Version of the Application

In this exercise, let's explore how to deploy a new version of the application. In the last exercise, we setup a GitHub webhook. This allows OpenShift to be notified anytime a new commit is made to your GitHub project.

## Push a New Version of Example Health to GitHub

    1. Access your GitHub repository

        It should look something like: github.com/svennam92/node-s2i-openshift

        TODO: Image

    2. Access the front-end HTML files

        Navigate to `site/public/login.html`

    3. Hit the "Pen" icon on the top-right to start editing this file.

    4. Change the `Fictionalname` of the company from "Example Health" to a name of your choice.

    5. Commit the changes directly to the master branch.

You've just updated the "Example Health" application. GitHub fires off the webhook you've configured, and alerts OpenShift of the new version.

## Verify the new build in OpenShift

    1. Navigate back to your OpenShift web console.

    2. Navigate to `Overview`.

    3. A new build should be in progress, or completed. Wait for the build to complete and access the application using the same route. You should be able to see the changes to your application!

## Understanding What Happened

When you commit a new change to the repository on GitHub, it fires the web hooks you've configured in your GitHub repository settings. This tells OpenShift to pull the latest version of the code from GitHub, build it into an image (using the built-in OpenShift S2I builder), and start a new build to deploy it into OpenShift.

By default, builds in OpenShift use a "Rolling" strategy. This will wait for the new pods to pass the readiness check, and then bring down the old pods. In this strategy, both versions may temporarily be running at the same time, and users shouldn't see any downtime. However, this isn't always ideal - for example, if two versions apps should never run concurrently.

## Customizing Deployment Strategies

Let's customize the deployment strategy.

TODO

### [Continue to Exercise 4](../exercise-4/README.md)
