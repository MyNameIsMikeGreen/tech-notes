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

# Resources
* Pluralsight: "Kubernetes for Developers: Core Concepts"
