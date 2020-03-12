# Exercise 7: Deploying a Full Project

Exercise 7 is a full happy path deployment of a full project. You'll be deploying an Open Liberty application
that outputs some data. You'll be setting up your environment then deploying it directly to OpenShift.

Below please find the architecture of the project.

![](../.gitbook/assets/-Lti9WcAPYU97e8yFB7v-image.png)

## Prebuilding an Image with local Code

There is an image on DockerHub with all required tools. In order to use local IDEs and editors to modify code and configuraton files a Docker volume is used. This option works only for Mac and Linux.

### Step 1: Run these commands in a terminal

```bash
git clone https://github.com/IBM/openshift-on-ibm-cloud-workshops.git
cd openshift-on-ibm-cloud-workshops
ROOT_FOLDER=$(pwd)
docker run -v $ROOT_FOLDER/:/cloud-native-starter -it --rm nheidloff/openshift-workshop-tools:v1
```

### Step 2: Inside your running Docker image you can access your the local project

You should see the prompt like this `root@3f46c41f7303:/usr/local/bin#`, now run the following instructions:

```bash
cd /cloud-native-starter/
ls
ROOT_FOLDER=$(pwd)
```

Note: With the `--rm` option in the docker run command the container is deleted once you exit. This is intended.

### Step 3: Login to your OpenShift Cluster

To launch the OpenShift web console, navigate to the [IBM Cloud Clusters Dashboard](https://cloud.ibm.com/kubernetes/clusters), find your cluster, and click on it.

![Clusters Dashboard](../.gitbook/assets/clusters-dashboard.png)

Click on `OpenShift web console` on the top right to launch the web console.

![Launch the OpenShift web console](../.gitbook/assets/launch-console.png)

Once in the OpenShift web console, click on the email/ID in the upper right. Choose the *Copy Login Command* option.

![Copy the login credentials](../.gitbook/assets/copy-login-command.png)

Verify 'kubectl' CLI

```bash
kubectl get pods
```

## Deploying to OpenShift

### Overview

In this part of the exercise we will work in the OpenShift Web Console and with the OpenShift CLI. The following image is a simplified overview of the topics of that lab.

This has two parts:

1. Build and save the container image to OpenShift internal Container Repository

- We will create a OpenShift project
- We will define a build config for OpenShift
- We will build with the build Pod inside OpenShift and save container image to the internal OpenShift container registry

2. Deploy the application to the cluster and expose the service

- You will define and apply a deployment configuration (`yaml`) to create a Pod with your microservice
- You will define a service which routes requests to the Pod with your microservice
- You will expose the service

The following gif is an animation of the simplified steps above in a sequence.

![Building the project in two steps](../.gitbook/assets/assets_-LtBxDkdPh1ZKmLAzW5v_-LtiA8xoR9evM5RpWqWE_-LtiBT_4lwRVfgHGh0I2_lab-4-overview.gif)

#### Step 1: Creating and Pushing Image to the Internal Registry

We need an OpenShift project, this is simply put equivalent to a Kubernetes namespace plus OpenShift security. Let us create one.

Make sure you are logged on to your OpenShift cluster.

*Note:* A project allows a community of users to organize and manage their content in isolation from other communities.

```bash
cd ${ROOT_FOLDER}/deploying-to-openshift
oc project # This maybe 'default' or something else. Verify step here.
oc new-project cloud-native-starter
```

#### Step 2: Build and Save the Container Image in the OpenShift Container Registry

Now we want to build and save a container image in the OpenShift Container Registry. We use these commands to do that:

Defining a new build using 'binary build' and the Docker strategy.

```bash
oc new-build --name authors --binary --strategy docker
```

Starting the build process on OpenShift with our defined build configuration. (oc start-build documentation)

```bash
oc start-build authors --from-dir=.
```

Verify the build in the OpenShift web console

- Select the 'cloud-native-starter' project in 'My Projects'
- Open 'Builds' in the menu and then click 'Builds'
- Select 'Last Build' (#1)
- Open 'Logs'
- Inspect the logs

Verify the container image in the Open Shift Container Registry UI

- Select the 'default' project
- Expand DEPLOYMENT 'registry-console' in 'Overview' and click on the URL in 'Routes - External Traffic'
- In the container registry you will find the 'authors' image and you can click on the latest label.

#### Step 3: Apply the deployment.yaml

This deployment will deploy a container to a Pod in Kubernetes. For more details we use the Kubernetes documentation for Pods.

> A Pod is the basic building block of Kubernetes–the smallest and simplest unit in the Kubernetes object model that you create or deploy. A Pod represents processes running on your Cluster .

Here is a simplified image for that topic. The deployment.yaml file points to the container image that needs to be instantiated in the pod.

![The pod is created from the internal container repository](../.gitbook/assets/assets_-LtBxDkdPh1ZKmLAzW5v_-LtiA8xoR9evM5RpWqWE_-LtiDjyDd9SpDsBUKfBI_image.png)

Let's start with the deployment yaml. For more details see the [Kubernetes documentation](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) for deployments.

The definition of `kind` defines this as a `Deployment` configuration.

```yaml
kind: Deployment
apiVersion: apps/v1beta1
metadata:
  name: authors
```

Inside the spec section we specify an app name and version label.

```yaml
spec:
  ...
  template:
    metadata:
      labels:
        app: authors
        version: v1
```

Then we define a name for the container and we provide the container image location, e.g. where the container can be found in the Container Registry.

The `containerPort` depends on the port definition inside our `Dockerfile` and in our `server.xml`.

```yaml
spec:
      containers:
      - name: authors
        image: authors:1
        ports:
        - containerPort: 3000
        livenessProbe:
```

Here is a complete `deployment.yaml` that you'll be deploying.

```yaml
kind: Deployment
apiVersion: apps/v1beta1
metadata:
  name: authors
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: authors
        version: v1
    spec:
      containers:
      - name: authors
        image: image-registry.openshift-image-registry.svc:5000/cloud-native-starter/authors:latest
        ports:
        - containerPort: 3000
        livenessProbe:
          exec:
            command: ["sh", "-c", "curl -s http://localhost:3000/"]
          initialDelaySeconds: 20
        readinessProbe:
          exec:
            command: ["sh", "-c", "curl -s http://localhost:3000/health | grep -q authors"]
          initialDelaySeconds: 40
      restartPolicy: Always
```

#### Step 4: Apply the deployment

1. Ensure you are in the `${ROOT_FOLDER}/2-deploying-to-openshift/deployment`

```bash
cd ${ROOT_FOLDER}/2-deploying-to-openshift/deployment
```

2. Copy the `template.deployment.yaml` to `deployment.yaml`.

3. Open the file with `nano` and edit the `<projectname>` with `cloud-native-starter`. Also change the `image:` line to reflect

```yaml
        image: docker-registry.default.svc:5000/cloud-native-starter/authors:latest
```

4. Apply the deployment to OpenShift

```bash
oc apply -f deployment.yaml
```

#### Step 5: Verify the deployment in OpenShift

- Open your OpenShift Web Console
- Select the Cloud-Native-Starter project and examine the deployment
- Click on #1 to open the details of the deployment
- In the details you find the 'health check' we defined before

#### Step 6: Apply the `service.yaml`

After the definition of the Pod we need to define how to access the Pod. For this we use a service in Kubernetes. For more details see the Kubernetes documentation for service.

> A Kubernetes Service is an abstraction which defines a logical set of Pods and a policy by which to access them - sometimes called a micro-service. The set of Pods targeted by a Service is (usually) determined by a Label Selector.

In the service we map the `NodePort` of the cluster to the port `3000` of the Authors microservice running in the authors Pod, as we can see in the following picture.

![Exposing the pod with the service](../.gitbook/assets/assets_-LtBxDkdPh1ZKmLAzW5v_-LtiA8xoR9evM5RpWqWE_-LtiE_wmpm63DSTJBa3Q_image.png)

In the `service.yaml` we see a selector of the pod using the label 'app: authors'.

```yaml
kind: Service
apiVersion: v1
metadata:
  name: authors
  labels:
    app: authors
spec:
  selector:
    app: authors
  ports:
    - port: 3000
      name: http
  type: NodePort
```

#### Step 7: Apply the service and expose it to the real world

- Apply the service to OpenShift

```bash
oc apply -f service.yaml
```

Using `oc expose` we create a Route to our service in the OpenShift cluster. (oc expose documentation)

```bash
oc expose svc/authors-bin
```

#### Step 8: Test the microservice

Execute this command, copy the URL to open the Swagger UI in browser

```bash
echo http://$(oc get route authors-bin -o jsonpath={.spec.host})/openapi/ui/
```

![Swagger in a browser](../.gitbook/assets/assets_-LtBxDkdPh1ZKmLAzW5v_-LtiA8xoR9evM5RpWqWE_-LtiEitfev7y0iBe0AoE_image.png)

2. Execute this command to verify the output:

```bash
curl -X GET "http://$(oc get route authors-bin -o jsonpath={.spec.host})/api/v1/getauthor?name=Niklas%20Heidloff" -H "accept: application/json"
```

3. The output should look something the following:

```bash
{"name":"Niklas Heidloff","twitter":"https://twitter.com/nheidloff","blog":"http://heidloff.net"}
```

Thanks so much for running this full workshop, we hope you've learned something. If you can reach out to the TA or Workshop Presenter and say you've completed with any feedback you may have.
