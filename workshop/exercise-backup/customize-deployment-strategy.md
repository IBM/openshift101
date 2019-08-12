# Backup Exercises

By default, builds in OpenShift use a "Rolling" strategy. This will wait for the new pods to pass the readiness check, and then bring down the old pods. In this strategy, both versions may temporarily be running at the same time, and users shouldn't see any downtime. However, this isn't always ideal - for example, if two versions apps should never run concurrently.

## Customizing Deployment Strategies

Let's customize the deployment strategy.

TODO
