# Exercise 1: Deploy a Java application with a Docker Image

In this exercise, you'll deploy a simple Java microservice - "Authors" from a public image registry to OpenShift. You can find the image on Docker Hub here: [https://hub.docker.com/r/nheidloff/authors](https://hub.docker.com/r/nheidloff/authors)

## Deploy Authors application

Access your cluster on the [IBM Cloud clusters dashboard](https://cloud.ibm.com/kubernetes/clusters). Click the `OpenShift web console` button on the top-right. (This is a pop-up so you'll need to white list this site.)

Create a project, you can title it whatever you like, we suggest "example-authors."

![Create Project](../.gitbook/assets/createproject.png)

Click on your new project. You should see a view that looks like the image below. Click on the *Deploy Image* button on the main screen, or click on the *Add to Project* button and choose *Deploy Image* from there.

![Click 'Deploy Image'](../.gitbook/assets/assets_-LtBxDkdPh1ZKmLAzW5v_-LtiA8xoR9evM5RpWqWE_-LtiGF9_KJN2mKkBlNku_image.png)

At the pop up, choose the *Image Name* option. Enter `nheidloff/authors:v1` as the image name and click the search icon. Once the image metadata loads click the *Deploy* button.

![Add image name](../.gitbook/assets/assets_-LtBxDkdPh1ZKmLAzW5v_-LtiA8xoR9evM5RpWqWE_-LtiGIGDIs2-4z0I2z0H_image.png)

At the application overview page, click the *Create Route* option. This will give our application an external URL.

![Create a route](../.gitbook/assets/assets_-LtBxDkdPh1ZKmLAzW5v_-LtiA8xoR9evM5RpWqWE_-LtiGPdbdT1F2RjVG_eK_image.png)

The default options are sufficient, scroll down to the bottom and click the *Create* button.

![Click Create](../.gitbook/assets/assets_-LtBxDkdPh1ZKmLAzW5v_-LtiA8xoR9evM5RpWqWE_-LtiGSVIhmj0Kn3iuPoC_image.png)

Try to launch the application by copying the route's URL and appending `/openapi/ui`.

![Copy the URL](../.gitbook/assets/assets_-LtBxDkdPh1ZKmLAzW5v_-LtiA8xoR9evM5RpWqWE_-LtiGVIO2z7C4sIM2eDq_image.png)

{% hint style="info" %}
Add `/openapi/ui` to the URL!
{% endhint %}

The Open API user interface for our *Authors* service will load. We can test the application by clicking the **GET /v1/getauthor** API. Choose the *Try it out* button and entering "Niklas Heidloff" as the query.

![Open the Open API UI](../.gitbook/assets/assets_-LtBxDkdPh1ZKmLAzW5v_-LtiA8xoR9evM5RpWqWE_-LtiGZZBOVOQDdQ5b-96_image.png)

**Congratulations!** You just deployed an image from a public registry aviable from Docker Hub.
