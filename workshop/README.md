## OpenShift 101: Learn the Basics of Red Hat OpenShift on IBM Cloud

A recent [study](https://github.com/svennam92/openshift101/tree/ab7f1406831de9aa1a60da349dd5bff8d11f7e13/workshop/study/README.md) by McKinsey & Company reveals that only 20 percent of enterprise applications have moved to the cloud. We believe that a hybrid cloud approach, built on open source and a vibrant open ecosystem, is the best way to move the remaining 80 percent.

Red Hat OpenShift represents a common platform, based on the industry-standard Kubernetes, that allows you to build on premises, on the IBM Cloud, or on any other leading cloud platform. You want freedom of choice; Red Hat OpenShift offers exactly that.

The goals of this workshop are:

* To familiarize the reader with OpenShift
* Deploy a Node.js application to OpenShift
* Use OpenShift's features to monitor, scale the application

### About this workshop

The introductory page of the workshop is broken down into the following sections:

* [Agenda](#agenda)
* [Compatability](#compatability)
* [Credits](#credits)

## Agenda

|   |   |
| - | - |
| [Exercise 1: Deploying an application](exercise-1/README.md) | Use Source-to-Image (s2i) to deploy a Node.js application |
| [Exercise 2: Logging and monitoring](exercise-2/README.md) | Explore the deployed application with OpenShift's console logs |
| [Exercise 3: Metrics and dashboards](exercise-3/README.md) | View application metrics with Grafana, Prometheus, and Alert Manager |
| [Exercise 4: Scaling the application](exercise-4/README.md) | Set resource limits and scale the application with the Horizontal Pod Autoscaler |
| [Exercise 5: Health checks](exercise-5/README.md) | Explore Readiness and Liveness Probes |
| [Exercise 6: Deploying the app using the CLI](exercise-6/README.md) | Get familiar with the `oc` CLI to deploy another sample application |

## Compatability

This workshop has been tested on the following platforms:

* **macOS**: Mojave (10.14), Catalina (10.15)

## Credits

Many folks have contributed to help shape, test, and contribute the workshop.

* [Spencer Krum](https://github.com/nibalizer)
* [JJ Asghar](https://github.com/jjasghar)
* [Tim Robinson](https://github.com/timroster)
* [Mofi Rahman](https://github.com/moficodes)
* [Sai Vennam](https://github.com/svennam92)
* [Steve Martinelli](https://github.com/stevemar)
* [Ram Vennam](https://github.com/rvennam)
* [Remko De Knikker](https://github.com/remkohdev)
* [Alex Parker](https://github.com/ajp-io)
