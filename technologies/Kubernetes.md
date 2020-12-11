Kubernetes
================

# Overview
Kubernetes is a container orchestration system. It facilitates the deployment and management of applications encapsulated in a container technology (e.g. Docker) across multiple virtual or physical machines.

Given multiple places containerised applications can run (nodes), Kubernetes decides which of the nodes has the capacity to have the container deployed onto it. Once running, Kubernetes monitors the health of the container so that it can restart it in-case of a crash or if the resources of the node are running low. Kubernetes also has many other features to increase reliability of a production application.

# Components

## Primary Architecture
| Term | Explanation | Synonyms |
|------|-------------|----------|
| Kubernetes |  | K8s |
| Application | The system we developed to perform business logic. | App, Program
| Container | An executable package of an application and its dependencies. |  |
| Pod | A collection of closely related containers to be used as a single unit. |  |
| Node | A machine, physical or virtual, to run a selection of pods on. |  |
| Cluster | A collection of nodes managed by Kubernetes. |  |
| Master Node | The node Kubernetes resides in. Manages all other nodes in the cluster. |  |
| Worker Node | Non-master nodes. Run pods containing application containers. |  |

## Master Node
The master node has multiple components to assist in the management of the cluster. The primary components include:
* etcd Store
  * Data store for all values the master node needs to track over time.
* Controller Manager
  * Handles requests for action.
  * Calls out to the Scheduler to execute an action is necessary.
* Scheduler
  * Determines when pods need to be interacted with (killed, started, etc...)
* API Server
  * Provides an interface to the cluster externally.
  * Often called using the `kubectl` command-line tool.

# Worker Node
Each worker node contains several administration components to support it in running pods. These include:
* Kubelet
  * A component that manages communication to/from the master node.
* Container runtime
  * The executable tools necessary to run containers.
  * Most commonly, this will be the Docker runtime.
* Kube-Proxy
  * Provides the pod with networking capabilities.

# Deployments
To understand how to create and update instances of an application, we must supply a **Deployment Configuration**.

The master node reads all deployment configurations and decides on which nodes to deploy instances. Once deployed, the **Kubernetes Deployment Controller** then assumes responsibility for monitoring the health of the instance(s) and redeploys them on other nodes if they become unhealthy.

A deployment configuration consists of a unique name, and an app image. For example:

```
kubectl create deployment [kubernetes-bootcamp] --image=gcr.io/google-samples/kubernetes-bootcamp:v1
```

Note: If not hosted in Docker Hub, the image path must be a complete URL.

## Scaling

## Updating

When changing the image in each deployment, every pod is updated as a rolling deployment to prevent any downtime to the application. Assuming the deployment has multiple pods, there will exist at least one version of the old image until a new one is available to take over the load; pods with the old image will update once pods with the new image have successfully started.

Running a rolling update is as simple as changing the image assigned to a deployment.

# Services
A service is an abstraction layer for a group of similar pods. This abstraction layer is useful when networking between different parts of a system (e.g. frontend and backend) as the frontend can reference the service rather than the individual pods - the frontend is therefore unaffected even if the backend pods are restarted or redeployed. It also provides a public interface to the instances from outside of the cluster.

Pod groupings for services are usually defined using pod labels. A pod label is a simple key-value pair which can contain any data that may be helpful in categorising a pod. For instance, it may simply be the human-readable name of the application (e.g. `"app-name": "xyz-server"`) or it could hold the name of the environment (e.g. `"environment": "dev"`). A pod may have as many labels as desired.

Services are created using the following command:

```
kubectl expose deployment/[DEPLOYMENT_NAME] --type=["ClusterIP", "NodePort", "LoadBalancer", or "ExternalName"]
```

**TO FIND OUT:** How are labels integrated with the expose command?

# Networking
Pods that are running inside Kubernetes are running on a private, isolated network. By default they are visible from other pods and services within the same Kubernetes cluster, but not outside that network.

`kubectl` utilises the public API of the master node to interact with the cluster from outside the cluster itself.

`kubectl proxy` can be used to grant direct access to other pods as if the local computer were part of the private network.

# kubectl

## Commands
**TO-DO**

* `kubectl get` - list resources
* `kubectl describe` - show detailed information about a resource
* `kubectl logs` - print the logs from a container in a pod
* `kubectl exec` - execute a command on a container in a pod


# Resources
* Pluralsight: "Kubernetes for Developers: Core Concepts"
* https://kubernetes.io/docs/tutorials/
