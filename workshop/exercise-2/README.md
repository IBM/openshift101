# Exercise 2 - Create a Sample Node.js Application

In this exercise, you'll deploy a simple Node.js Express application - "Example Health". Example health is a simple UI for a patient health records system. We'll use this example to demonstrate key OpenShift features throughout this workshop.

1. Open the Web Console

    Connect to the OpenShift web console. You can find the URL in two ways:

    a. Get the Master URL for your cluster from the `ibmcloud` utility

    ```shell
    ibmcloud ks cluster get $MYCLUSTER  | grep 'Master URL'
    ```

    b. Access your cluster on the [IBM Cloud clusters dashboard](https://cloud.ibm.com/kubernetes/clusters):

2. Fork the Sample Application GitHub repository
    
    The sample application lives here: https://github.com/IBM/node-s2i-openshift

    You'll want your own fork of the GitHub repository so we can make changes to the project. If you don't already have a GitHub account, make one -- it's quick and free!

    Navigate to the sample repository click on the Fork button.

    ![fork](./images/fork.png)

    Select your github user name from the pop-up window.

3. Deploy *Example Health* from your new repository

    To deploy your just-forked repository, go to the Web Console for your OpenShift cluster and create a project:

    ![create project](./images/createproject.png)

    Click on your new project. You should see a view that looks like this:

    ![project](./images/projectview.png)

    Click on the browse catalog button to see the images available to build with and scroll down to the Node image. Click on the 'Node.js' icon.

    ![node](./images/node.png)

    Click through to the second step for configuration, and choose advanced options ( a hyperlink on the bottom line )

    ![config](./images/advanced.png)

    You'll see and advanced form like this:

    ![form](./images/node-advanced-form.png)

    Enter your forked Git Repository URL and `/site` for the Context Dir. Click 'Create' at the bottom of the window to build and deploy the application.
    
    TODO: Follow the *obvious* steps on the website pointing you to create a webhook on GitHub.

    Scroll through to watch the build deploying:

    ![build](./images/build.png)

    When the build has deployed, click the External Traffic Route, and you should see the login screen:

    ![login](./images/login.png)

    You can enter any strings for username and password, for instance test/test ... because the app is just running in demo mode.

    And you've deployed a node app to kubernetes using OpenShift S2I.

## Understanding What Happened

TODO: Explain OpenShift S2I

### [Continue to Exercise 3 - Deploy a New Version of the App](../exercise-3/README.md)
