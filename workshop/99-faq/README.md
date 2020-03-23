# FAQ

This page would list all know questions and potential problems, with possible answers or remedies.

## So what is OpenShift

To quote Wikipedia:

> OpenShift is a family of containerization software developed by Red Hat. Its flagship product is the OpenShift Container Platform-an on-premises platform as a service built around Docker containers orchestrated and managed by Kubernetes on a foundation of Red Hat Enterprise Linux.
> The Openshift UI has various functionalities, allowing one to monitor the container resources, container health, the nodes the containers reside on, IP addresses of the nodes, etc. The key store can be accessed via the Secrets in Openshift. The OC CLI command line tool also offers similar functionalities.

But the stort of it? It's a abstraction layer **ON TOP** of Kubernetes. It's a way to empower Developers to deploy code and not worry about a lot of the underlying ecosystem. This workshop should show you the happy path to take advantage of most of the best parts of OpenShift and what it can offer.

## Upgrade your Trial account

In 2018, IBM Cloud moved to a "Lite" account. If you have an old "Trial" account that expired after 30 days you should go to <https://cloud.ibm.com/registration/startUpgradeToLite> to freely convert your account to a "Lite" account.

## I can't create a new account

Ask for whitelisting, or use your own smart phone with data plan (do not use local WiFi).

## I don't see OpenShift's Web Console

Try restarting the VPN pod with the following command (log in to the environement using CLI token):

```bash
kubectl delete pod -l app=vpn -n kube-system --wait=false
```

## I see an error while deploying my image to an internal container registry

Try restarting the VPN with the following command:

```bash
kubectl delete pod -l app=vpn -n kube-system --wait=false
```

If that doesn't work please rebuild a cluster, or ask to get a new cluster.
