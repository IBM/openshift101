# Setup CLI Access for the Cluster

You must already have an IBM account, with a cluster created or assigned to you as documented in [Get Started](../../GETSTARTED.md).

## Install OpenShift CLI tools

1. Download and unpack OpenShift cli tools. The `oc` utility is your main gateway into OpenShift. We'll add them to your path in a convenient location. Please double check the latest release here: [OpenShift Origin Releases](https://github.com/openshift/origin/releases/)

   a. Download tarball of the tools

   ```text
    wget https://github.com/openshift/origin/releases/download/v3.11.0/openshift-origin-client-tools-v3.11.0-0cbc58b-linux-64bit.tar.gz
   ```

   b. Unpack

   ```text
    tar -xvzf openshift-origin-client-tools-v3.11.0-0cbc58b-linux-64bit.tar.gz
   ```

   c. Rename for ease of use

   ```text
    mv openshift-origin-client-tools-v3.11.0-0cbc58b-linux-64bit ${HOME}/oc-cli
   ```

   d. Set Path. Note that if you restart your cloud shell, you may need to re-run this command.

   ```text
    export PATH=${PATH}:${HOME}/oc-cli
   ```

   e. Verify the utility is available by using `which` and the help

```text
    which oc
```

```text
    oc help
```

## Access the OpenShift Web UI

1. Launch the OpenShift web console.
   1. Navigate to the [IBM Cloud Clusters Dashboard](https://cloud.ibm.com/kubernetes/clusters)
   2. Find your cluster and click it
   3. Click `OpenShift Web Console` on the top right
2. Once in the console, select your name/id in the upper right, click. Scroll down to 'Copy Login Command' and click it.

## Access your cluster using OpenShift client utils

1. Paste the login command you copied from the Web UI.

```text
oc login https://c100-e.us-east.containers.cloud.ibm.com:39813 --token=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

You should see a success message.

1. Validate access to your cluster.

   a. View nodes in the cluster.

   ```text
   oc get node
   ```

   b. View services, deployments, and pods.

   ```text
   oc get svc,deploy,po --all-namespaces
   ```

   c. View all OpenShift projects

   ```text
   oc get projects
   ```

## [Continue to Exercise 1](exercise-1/README.md)
