# FAQ

This page would list all know questions and potential problems, with possible answers or remedies.

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
