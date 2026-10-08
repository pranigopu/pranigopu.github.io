<h1>MASTR</h1>

> ***Miscellaneous Alphabetically-Sorted Technical References***
> 
> I don't know how to categorise these 😄

---

**Contents**:

- [cAdvisor (Container Advisor)](#cadvisor-container-advisor)
- [ClickHouse Cloud Service](#clickhouse-cloud-service)
  - [ClickHouse Cloud Service Replica](#clickhouse-cloud-service-replica)
  - [ClickHouse Cloud Service Automatic Idling](#clickhouse-cloud-service-automatic-idling)
- [CRI (Container Runtime Interface) Container Metrics](#cri-container-runtime-interface-container-metrics)
- [cgroup (Control Group)](#cgroup-control-group)
- [Helm Chart](#helm-chart)
- [kubectl port-forward](#kubectl-port-forward)
- [kubelet](#kubelet)
- [Kubernetes Control Plane](#kubernetes-control-plane)
  - [kube-apiserver](#kube-apiserver)
  - [etcd](#etcd)
  - [kube-scheduler](#kube-scheduler)
  - [kube-controller-manager](#kube-controller-manager)
  - [cloud-controller-manager (optional)](#cloud-controller-manager-optional)
- [Kubernetes Informer](#kubernetes-informer)
- [Kubernetes Lease](#kubernetes-lease)
- [Kubernetes Metrics Adapters](#kubernetes-metrics-adapters)
- [Kubernetes Operator](#kubernetes-operator)
- [Kubernetes Service](#kubernetes-service)
  - [`port`](#port)
  - [`targetPort`](#targetport)
- [Kubernetes StatefulSet](#kubernetes-statefulset)
- [Prometheus `job` and `instance`](#prometheus-job-and-instance)
  - [Job](#job)
  - [Instance](#instance)
  - [Additional Point](#additional-point)
- [Prometheus Kubernetes Service Discovery (SD)](#prometheus-kubernetes-service-discovery-sd)
- [Virtual CPU (vCPU)](#virtual-cpu-vcpu)
- [Virtual Private Cloud (VPC)](#virtual-private-cloud-vpc)
- [VPC Peering](#vpc-peering)

---

# cAdvisor (Container Advisor)
A light-weight, open-source agent that provides container users an understanding of the resource usage and performance characteristics of their running containers. It is a running daemon that collects, aggregates, processes, and exports information about running containers. Specifically, for each container it keeps resource isolation parameters, historical resource usage, histograms of complete historical resource usage and network statistics. This data is exported container and machine-wide. cAdvisor has native support for Docker containers and should support just about any other container type out of the box.

> **Reference**: [`google`/`cadvisor`, **github.com**](https://github.com/google/cadvisor)

As mentioned, cAdvisor is a daemon that runs on a host and automatically discovers every container running on that machine. Once it detects a container, it immediately begins collecting resource usage statistics without requiring any configuration inside the container itself.

> **Reference** ["Docker Monitoring with cAdvisor: The Definitive Guide", **www.dash0.com/guides**](https://www.dash0.com/guides/cadvisor-docker-monitoring)

In EKS, cAdvisor runs on a node-local kubelet (kubelets are discussed here: ["Kubelet"](#kubelet)).

# ClickHouse Cloud Service
A set of replicas that share a common endpoint.

**NOTE**: *Services can be scaled horizontally and vertically.*

> **Reference**: [*ClickHouse Cloud*, **langfuse.com/handbook/product-engineering/infrastructure**](https://langfuse.com/handbook/product-engineering/infrastructure/clickhouse)

## ClickHouse Cloud Service Replica
***Or simply replica***

An individual instance within a Service. Matches to a Kubernetes Pod.

> **Reference**: [*ClickHouse Cloud*, **langfuse.com/handbook/product-engineering/infrastructure**](https://langfuse.com/handbook/product-engineering/infrastructure/clickhouse)

Essentially, it is a compute node within a service.

## ClickHouse Cloud Service Automatic Idling
In the Settings page, you can also choose whether or not to allow automatic idling of your service when it is inactive for a certain duration (i.e. when the service is not executing any user-submitted queries). Automatic idling reduces the cost of your service, as you are not billed for compute resources when the service is paused, although storage remains fully intact. The service wakes up automatically the moment a new query or connection attempt is made.

> **Reference**: ["Automatic idling", **clickhouse.com/docs/cloud/features/autoscaling**](http://clickhouse.com/docs/cloud/features/autoscaling/idling)

# CRI (Container Runtime Interface) Container Metrics
Low-level performance and resource usage statistics, such as CPU, memory, and disk I/O-collected directly from container runtimes (like containerd or CRI-O) instead of via cAdvisor. Enabling `PodAndContainerStatsFromCRI` allows the kubelet to poll runtimes directly, improving performance for dense nodes.

> **Reference**: [CRI Pod & Container Metrics, **kubernetes.io/docs/reference/instrumentation**](https://kubernetes.io/docs/reference/instrumentation/cri-pod-container-metrics/)

# cgroup (Control Group)
A Linux kernel feature that organizes processes into hierarchical groups to limit, account for, and isolate resource usage (CPU, memory, disk I/O, network). They ensure system stability by preventing single processes or containers from exhausting hardware resources. Key to containerization (Docker, Kubernetes) and systemd, they offer v1 (multiple hierarchies) and v2 (unified) versions.

> **Reference**: [cgroups, **en.wikipedia.org**](https://en.wikipedia.org/wiki/Cgroups)

# Helm Chart
A **Helm chart** is a packaged Kubernetes application - a collection of Go templates for Kubernetes manifests, bundled with a `values.yaml` defaults file. When `helm upgrade --install` is run, Helm renders those templates into actual Kubernetes YAML by substituting values, then applies the result to the cluster.

> **Reference**: [Helm Documentation - Charts](https://helm.sh/docs/topics/charts/)

# kubectl port-forward
Forward one or more local ports to a pod. This means any request to a local port (i.e. a port in your local machine) is forwarded (i.e. routed) to a pod, and the pod's response is what is received by the IP through which the request is made. If there are multiple pods matching the criteria, a pod will be selected automatically. The forwarding session ends when the selected pod terminates, and a rerun of the command is needed to resume forwarding.

---

Syntax:

```sh
kubectl port-forward TYPE/NAME [options] [LOCAL_PORT:]REMOTE_PORT [...[LOCAL_PORT_N:]REMOTE_PORT_N]
```

> **NOTE**:
>
> - `REMOTE_PORT` => Port on which the pod can be accessed within the Kubernetes cluster
> - `LOCAL_PORT` => Port in the local system on which the request is to be made
> - By default, the local IP address is `localhost` unless otherwise specified

Examples:

```sh
# Listen on port 8888 locally, forwarding to 5000 in the pod
kubectl port-forward pod/mypod 8888:5000

# Listen on port 8888 on all local addresses, forwarding to 5000 in the pod
kubectl port-forward --address 0.0.0.0 pod/mypod 8888:5000
```

> **Reference**: [*kubectl port-forward*, **kubernetes.io/docs/reference/kubectl/generated**](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_port-forward/)

# kubelet
A kubelet in Kubernetes is the primary node agent that runs on each node. It manages container lifecycle, facilitates communication between the control plane and its respective node, and kubelet instances together enable deployment of containerized applications across the cluster. In other terms, it is a service that runs on each worker node in a Kubernetes cluster and is resposible for managing the Pods and containers on a machine.

Its features include pod deployment, resource management, and health monitoring, all of which significantly improve the operational stability of a Kubernetes cluster. It registers the node with the apiserver using the hostname, an override flag, or specific logic for a cloud provider.

> **References**:
> 
> - [*What is Kubelet in Kubernetes?*, **geeksforgeeks.org/devops**](https://www.geeksforgeeks.org/devops/what-is-kubelet-in-kubernetes/)
> - [*Get kubelet’s metrics manually*, **yuki-nakamura.com**](https://yuki-nakamura.com/2023/10/15/get-kubelets-metrics-manually/)

# Kubernetes Control Plane
A Kubernetes cluster consists of a control plane and 1 or more worker nodes.

---

The control plane manages the overall state of the cluster.

Components of the control plane...

## kube-apiserver
The core component server that exposes the Kubernetes HTTP API. The Kubernetes API server validates and configures data for the API objects which include pods, services, replication controllers, and others. The API Server services REST operations and provides the frontend to the cluster's shared state through which all other components interact.

**Source**: [kube-apiserver, **kubernetes.io/docs/reference/command-line-tools-reference**](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver)

## etcd
Consistent and highly-available key value store for all API server data.

> **Reference**: [*Kubernetes Components*, **kubernetes.io/docs/concepts/overview**](https://kubernetes.io/docs/concepts/overview/components/)

## kube-scheduler
Looks for Pods not yet bound to a node, and assigns each Pod to a suitable node.

> **Reference**: [*Kubernetes Components*, **kubernetes.io/docs/concepts/overview**](https://kubernetes.io/docs/concepts/overview/components/)

## kube-controller-manager
Runs controllers to implement Kubernetes API behavior.

> **Reference**: [*Kubernetes Components*, **kubernetes.io/docs/concepts/overview**](https://kubernetes.io/docs/concepts/overview/components/)

## cloud-controller-manager (optional)
Integrates with underlying cloud provider(s).

> **Reference**: [*Kubernetes Components*, **kubernetes.io/docs/concepts/overview**](https://kubernetes.io/docs/concepts/overview/components/)

# Kubernetes Informer
A Kubernetes Informer is a client-side library that provides a mechanism to watch and react to changes in resources within a Kubernetes cluster. It enables developers to receive real-time updates about the state of various Kubernetes objects, such as pods, services, deployments, and more.

The Informer leverages the Kubernetes API server's watch functionality to establish a connection and receive continuous updates. It uses a combination of polling and event-driven mechanisms to stay synchronized with the cluster’s resources, ensuring that your application is always aware of the latest changes.

> **Reference**: [*Demystifying Kubernetes Informers*, **medium.com/@jeevanragula**](https://medium.com/@jeevanragula/demystifying-kubernetes-informer-streamlining-event-driven-workflows-955285166993)

# Kubernetes Lease
A Kubernetes Lease is a lightweight, built-in mechanism used to lock shared resources and coordinate activity between different components in a cluster. Distributed systems often have a need for leases, which provide a mechanism to lock shared resources and coordinate activity between members of a set (e.g. between pods in a DaemonSet, or between pods in a Deployment with multiple replicas, etc.). In Kubernetes, the lease concept is represented by Lease objects in the `coordination.k8s.io` API Group, which are used for system-critical capabilities such as node heartbeats and component-level leader election.

E.g.: *Lease for a service account to access the leader of a deployment's cluster.*

Kubernetes uses the Lease API to communicate kubelet node heartbeats to the Kubernetes API server. For every node, there is a Lease object with a matching name in the `kube-node-lease` namespace. Under the hood, every kubelet heartbeat is an update request to this Lease object, updating the `spec.renewTime` field for the Lease. The Kubernetes control plane uses the time stamp of this field to determine the availability of this Node.

> **Reference**: [*Leases*, **kubernetes.io/docs/concepts/architecture**](https://kubernetes.io/docs/concepts/architecture/leases/)

# Kubernetes Metrics Adapters
A metrics adapter connects your monitoring system to Kubernetes, allowing metrics to be surfaced through the Kubernetes metrics APIs. These adapters enable `HorizontalPodAutoscaling` to access a wider range of metrics beyond CPU and memory. Kubernetes supports various adapters depending on the monitoring backend you use. Some examples include Prometheus Adapter, Datadog Cluster Agent, and GCP Stackdriver Adapter.

> **Reference**: [*How to use Custom & External Metrics for Kubernetes HPA*, **medium.com/@livewyer**](https://medium.com/@livewyer/how-to-use-custom-external-metrics-for-kubernetes-hpa-fb2d407ba9bb)

# Kubernetes Operator
Kubernetes is designed for automation. Out of the box, you get lots of built-in automation from the core of Kubernetes. You can use Kubernetes to automate deploying and running workloads, and you can automate how Kubernetes does that.

Kubernetes' operator pattern concept lets you extend the cluster's behavior without modifying the code of Kubernetes itself by linking controllers to one or more custom resources. Operators are clients of the Kubernetes API that act as controllers for a Custom Resource.

> **Reference**: [*Operator pattern*, **kubernetes.io/docs/concepts/extend-kubernetes**](https://kubernetes.io/docs/concepts/extend-kubernetes/operator/)

---

A Kubernetes Operator is a method of packaging and deploying a Kubernetes-native application that is managed using Kubernetes APIs.

> **Reference**: [*Getting Started with AWS Distro for OpenTelemetry using EKS Add-Ons*, **aws-otel.github.io**](https://aws-otel.github.io/docs/getting-started/adot-eks-add-on/) <br> *Contains one relevant line: "The ADOT Operator is an implementation of a Kubernetes Operator, a method of packaging and deploying a Kubernetes-native application and managed using Kubernetes APIs"*

# Kubernetes Service
A resource to provide a stable endpoint for accessing pods running within a Kubernetes cluster.

*A Service eliminates the need to know IP addresses or ports.*

> **Reference**: [*Difference Between targetPort and port in Kubernetes Service Definition*, **www.baeldung.com/ops**](https://www.baeldung.com/ops/kubernetes-k8s-service-targetport-vs-port)

## `port`
A Service definition specifies the port number that the Service will listen on for incoming traffic using the port field. The Service uses this port to route traffic to the pods it is responsible for. In other words:

- `port` specifies the Service‘s listening port for incoming traffic
- `port` is used to listen for **incoming traffic from external clients**

> **Reference**: [*Difference Between targetPort and port in Kubernetes Service Definition*, **www.baeldung.com/ops**](https://www.baeldung.com/ops/kubernetes-k8s-service-targetport-vs-port)

## `targetPort`
In a Service definition, the `targetPort` field is set to the pod's port number that the Service is responsible for routing traffic to. By doing so, we can ensure that traffic is directed to the appropriate pods and that our Service functions as intended. In other words:

- `targetPort` field specifies the port number for routing traffic to the Service's pods
- `targetPort` is the Service's internal communication port <br> ... *with the pods responsible for handling that traffic*

> **Reference**: [*Difference Between targetPort and port in Kubernetes Service Definition*, **www.baeldung.com/ops**](https://www.baeldung.com/ops/kubernetes-k8s-service-targetport-vs-port)

> **NOTE**: `port` and `targetPort` can be the same.

# Kubernetes StatefulSet
StatefulSet represents a set of pods with consistent identities. Identities are defined as:

- **Network**: A single stable DNS and hostname
- **Storage**: As many VolumeClaims as requested

The StatefulSet guarantees that a given network identity will always map to the same storage identity.

> **Reference**: [*StatefulSet*, **kubernetes.io/docs/reference/kubernetes-api/apps**](https://kubernetes.io/docs/reference/kubernetes-api/apps/stateful-set-v1/)

# Prometheus `job` and `instance`
## Job
A job is a configured collection of targets that serve the same purpose (a replicated process, e.g. for scalability or reliability); Prometheus attaches the `job` label to every scraped series to record which configured job the target belongs to.

**Conceptual relevance**: Establishes `job` as a *configuration-time* grouping label - it names the `scrape_config` entry (`job_name`), not any property of the runtime instance itself. This underlies why job-level classification (e.g. node-bound vs. not) is a property of the scrape config, not of any single instance.

**Quote(s)**
> "job: The configured job name that the target belongs to."

> "Initially, aside from the configured per-target labels, a target's job label is set to the job_name value of the respective scrape configuration."

The `job` label's value is deterministic from the config alone - it is copied
directly from `job_name`, prior to any relabeling. This means every target
discovered under one `scrape_configs` entry shares the same `job` label by
default (absent `honor_labels` overriding it from scraped data), making `job`
the correct grouping key for reasoning about a job's overall scope (including
node-boundedness) independent of how many instances it currently has.

> **References**
> 
> - [*Jobs and instances*, **prometheus.io/docs/concepts**](https://prometheus.io/docs/concepts/jobs_instances/)
> - [*Configuration*, **prometheus.io/docs/prometheus/latest/configuration**](https://prometheus.io/docs/prometheus/latest/configuration/configuration/)

## Instance
An instance is a single scrapeable endpoint (usually one process); Prometheus attaches the `instance` label, set to the `<host>:<port>` part of the target's scraped URL, to identify which specific target within a job a series came from.

**Conceptual relevance**: Establishes `instance` as the *target-identity* label, one level below `job` in the label hierarchy - it's what distinguishes individual replicas of a job from one another, and is the label most directly tied to "which physical network endpoint produced this data," relevant to reasoning about node locality per-target.

**Quote(s)**
> "instance: The `<host>:<port>` part of the target's URL that was scraped."

> "After relabeling, the instance label is set to the value of `__address__` by default if it was not set during relabeling."

`instance` defaults to the resolved network address of the target
(`__address__`) after relabeling completes - meaning its value is a function
of whatever the SD mechanism and relabeling rules produced for `__address__`,
not a separately configured field. This is why inspecting relabel rules that
touch `__address__` (or explicitly set `instance` via `target_label:
instance`) is necessary to know what an instance label will actually resolve
to, and by extension, whether that resolved endpoint is node-local.

> **References**
>
> - [*Jobs and instances*, **prometheus.io/docs/concepts**](https://prometheus.io/docs/concepts/jobs_instances/)
> - [*Configuration*, **prometheus.io/docs/prometheus/latest/configuration**](https://prometheus.io/docs/prometheus/latest/configuration/configuration/)

## Additional Point
Both `job` and `instance` can be overridden by the scraped exposition data itself if `honor_labels: true` is set; the purpose of `honor_labels: true` allows metrics to specify the instance and job labels instead of pulling it from scrape_configs. This means a job/instance label observed in stored time series is not always traceable back to the static config alone.

# Prometheus Kubernetes Service Discovery (SD)
Kubernetes service discovery enables Prometheus to automatically discover scrape targets from Kubernetes API resources. More precisely, Kubernetes SD configurations allow retrieving scrape targets from Kubernetes' REST API and always staying synchronized with the cluster state. Unlike static configuration, Kubernetes SD continuously watches the Kubernetes API for changes and updates target groups dynamically as pods, services, and other resources are created, modified, or deleted. Kubernetes service discovery operates on different resource types, called roles. Each role discovers a different kind of Kubernetes resource and exposes different metadata labels.Kubernetes SD configurations allow retrieving scrape targets from Kubernetes' REST API and always staying synchronized with the cluster state.

---

`kubernetes_sd_configs` supports several discovery `role`s, including:

- `node` - enumerates Node objects cluster-wide
- `pod` - enumerates Pod objects cluster-wide
- `endpoints` - enumerates the backing endpoints of **Services** <br> "Backing endpoint" => Endpoints objects representing the backing pods for a service
    > **NOTE**: This API is deprecated in K8s v1.33+ in favor of EndpointSlice

Some other roles include:

- `endpointslices`
- `ingress`
- `service`

> **References**:
>
> - [*Kubernetes Service Discovery*, **deepwiki.com/prometheus/prometheus**](https://deepwiki.com/prometheus/prometheus/3.2.1-kubernetes-service-discovery)
> - [`<kubernetes_sd_config>`, *Configuration*, **prometheus.io/docs/prometheus/latest/configuration**](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#kubernetes_sd_config)

# Virtual CPU (vCPU)
A vCPU (virtual CPU) represents a portion of a physical processor core assigned to a Virtual Machine (VM) or container. A vCPU (virtual CPU) is not a physical object. It is a time-share on a physical CPU core, managed by a hypervisor. When a cloud provider provisions a VM with 8 vCPUs, what you are actually getting is 8 scheduled execution slots that the hypervisor will fulfill using whatever physical cores happen to be available at that moment.

On most cloud platforms, 1 vCPU maps to 1 hyperthread of a physical core. Hence, a 2 vCPU configuration provides 2 processing threads, offering performance roughly equivalent to a standard dual-core desktop processor

> **References**:
> 
> - [*What is a vCPU and How Do You Calculate vCPU to CPU?*, **www.datacenters.com/news**](https://www.datacenters.com/news/what-is-a-vcpu-and-how-do-you-calculate-vcpu-to-cpu)
> - [*The Difference Between a vCPU and a Dedicated CPU Core*, **openmetal.io/resources/blog**](https://openmetal.io/resources/blog/the-difference-between-a-vcpu-and-a-dedicated-cpu-core/)

# Virtual Private Cloud (VPC)
A virtual private cloud (VPC) is a virtual network dedicated to your AWS account. It is logically isolated from other virtual networks in the AWS Cloud. You can launch AWS resources, such as Amazon EC2 instances, into your VPC.

> **Reference**: [*What is VPC peering?*, **docs.aws.amazon.com/vpc/latest/peering**](https://docs.aws.amazon.com/vpc/latest/peering/what-is-vpc-peering.html)

# VPC Peering
VPC peering is a direct, private network connection between two isolated Virtual Private Clouds (VPCs). It allows resources in both networks to communicate seamlessly using private IP addresses as if they were in the same network. Traffic stays on the cloud provider's internal backbone, never traversing the public internet.

> **Reference**: [*What is VPC peering?*, **docs.aws.amazon.com/vpc/latest/peering**](https://docs.aws.amazon.com/vpc/latest/peering/what-is-vpc-peering.html)

---

The process of establishing VPC peering is as follows...

<details><summary><b>Initiation</b></summary>
<p>
The owner of one VPC (the requester) sends a peering request to the owner of another VPC (the accepter).
</p>
</details>

<details><summary><b>Approval</b></summary>
<p>
The administrator of the accepting VPC must log into their cloud console and accept the request to establish the connection.Routing: Administrators must explicitly update the route tables in both VPCs. You add the IP address range (CIDR block) of the peer VPC and point it to the peering connection.
</p>
</details>

<details><summary><b>Security</b></summary>
<p>
Peering simply enables the network path. Security groups and network access control lists (ACLs) still apply, meaning you must manually define which specific servers or services are allowed to talk to each other.
</p>
</details>

> **Reference**: [*AWS - VPC Peering*, **www.geeksforgeeks.org/devops**](https://www.geeksforgeeks.org/devops/amazon-vpc-concept-of-vpc-peering/)

---

Key limitations of VPC peering...

<details><summary><b>Non-transitive routing</b></summary>
<p>
VPC peering is a one-to-one relationship and does not support transitive routing. If VPC A peers with VPC B and VPC B peers with VPC C, VPC A does not automatically gain a route to VPC C. This makes large peering "meshes" harder to operate.
</p>
</details>

<details><summary><b>Overlapping CIDR blocks</b></summary>
<p>
You can’t create a peering connection if any IPv4 or IPv6 CIDR blocks overlap between the two VPCs, even if you only intend to use non-overlapping ranges. Plan address space early if you expect many VPCs.
</p>
</details>


<details><summary><b>Edge-to-edge routing limits</b></summary>
<p>
Peering is designed for VPC-to-VPC traffic, not as a way to "borrow" connectivity. Resources in one VPC can’t use the other VPC’s internet gateway, NAT device, VPN, Direct Connect, or similar edge resources through a peering connection. This matters when you’re trying to centralize egress or share connectivity.
</p>
</details>

<details><summary><b>Inter-region security group constraints</b></summary>
<p>
In AWS, you can’t reference a peer VPC’s security group across an inter-region peering connection. Instead, you typically use the peer VPC CIDR ranges in your rules, which can make least-privilege policy harder.
</p>
</details>

<details><summary><b>Inter-region MTU and jumbo frame considerations</b></summary>
<p>
Inter-region paths can impose smaller MTU limits than same-region connectivity. If you rely on jumbo frames, validate MTU and watch for fragmentation or drops.
</p>
</details>

<details><summary><b>Quotas and scaling pressure</b></summary>
<p>
AWS enforces quotas for peering connections, and large full-mesh designs can hit limits and create operational sprawl. If you’re trending toward "many VPCs, many peerings," evaluate a hub model (Transit Gateway) or service exposure (PrivateLink).
</p>
</details>

<details><summary><b>DNS resolution is optional, not automatic</b></summary>
<p>
If you expect private DNS names to resolve to private IPs across the peering link, you may need to enable DNS resolution settings on the peering connection and confirm VPC DNS attributes.
</p>
</details>

> **Reference**: [*What is VPC Peering?*, **www.kentik.com/kentipedia**](https://www.kentik.com/kentipedia/what-is-vpc-peering/)