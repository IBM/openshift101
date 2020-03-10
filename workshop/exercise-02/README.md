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

![Pods](../.gitbook/assets/view-logs.png)

You should be taken to something like the following. Scroll up and you should see the `DEBUG` like in the image. Scroll back down, and you should see a new line every second per the `curl` above.

![Logs](../.gitbook/assets/view-logs-details.png)

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

![Terminal](../.gitbook/assets/terminal-output.png)

## OpenShift Monitoring

When deploying new apps, making configuration changes, or simply inspecting the state of your cluster, the OpenShift monitoring dashboard gives you an overview of your running assets.

You can also dive in a bit deeper - the `Events` tab is very useful for identifying the timeline of events and finding potential error messages.

![View Details](../.gitbook/assets/event-details.png)

You'll want to refer to this view throughout the lab. Almost all actions we take in in OpenShift will result in an event being fired in this view. As it is updated real-time, it's a great way to track changes to state.
