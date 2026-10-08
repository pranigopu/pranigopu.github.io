[<< **Observability in AWS**](https://pranigopu.github.io/observability-in-aws/)

<h1>Metrics Collection in EKS</h1>

---

**Contents**:

- [Overview](#overview)
- [Resource Metrics Pipeline in Kubernetes](#resource-metrics-pipeline-in-kubernetes)
  - [About](#about)
  - [Statistics collection methods](#statistics-collection-methods)
    - [Method 1: metrics-server](#method-1-metrics-server)
    - [Method 2: cAdvisor](#method-2-cadvisor)
  - [Statistics Aggregation via Metrics API](#statistics-aggregation-via-metrics-api)
  - [Architecture](#architecture)
- [Full Metrics Pipeline in Kubernetes](#full-metrics-pipeline-in-kubernetes)
  - [About](#about-1)
  - [Scope \& Approach](#scope--approach)
- [Key Aspects of EKS Metrics](#key-aspects-of-eks-metrics)
  - [Metrics API](#metrics-api)
  - [Custom Metrics API](#custom-metrics-api)
  - [Kubernetes Control Plane Metrics](#kubernetes-control-plane-metrics)
  - [metrics-server (*implements* Metrics API)](#metrics-server-implements-metrics-api)
    - [Use in EKS](#use-in-eks)
    - [Not Installed in EKS by Default](#not-installed-in-eks-by-default)
  - [AWS Distro for OpenTelemetry (ADOT)](#aws-distro-for-opentelemetry-adot)
    - [ADOT Collector](#adot-collector)
    - [ADOT as an EKS Add-on](#adot-as-an-eks-add-on)
    - [Sending Metrics to Amazon Managed Service for Prometheus (AMP)](#sending-metrics-to-amazon-managed-service-for-prometheus-amp)
    - [Receivers in ADOT Collectors](#receivers-in-adot-collectors)
  - [CloudWatch Container Insights (*shortened to "Container Insights"*)](#cloudwatch-container-insights-shortened-to-container-insights)
    - [Deployment](#deployment)
  - [Metrics on EKS Fargate](#metrics-on-eks-fargate)

---

# Overview
This document describes how metric collection works in Amazon EKS, covering both the general Kubernetes mechanisms that underpin it and the EKS-specific tooling built on top of them. The purpose is to give us (as platform engineers) a clear mental model of the full metrics stack on EKS, from how raw container stats are gathered at the node level, through to how those metrics are exposed, aggregated, and consumed by autoscalers and monitoring systems.

---

**What is covered**:

[Resource Metrics Pipeline in Kubernetes](#resource-metrics-pipeline-in-kubernetes) establishes the foundation. It explains how the kubelet and cAdvisor collect raw CPU and memory statistics from containers on each node, how those statistics flow up through the metrics-server, and how they are exposed via the Metrics API (`metrics.k8s.io`). Understanding this pipeline is a prerequisite for everything that follows, since all higher-level EKS tooling either builds on it or works around its limitations.

[Full Metrics Pipeline in Kubernetes](#full-metrics-pipeline-in-kubernetes) covers what to reach for when the resource metrics pipeline is not enough. Where the resource pipeline is deliberately minimal (CPU and memory only, no history, autoscaling use cases only), the full pipeline unlocks richer, application-defined signals via the Custom Metrics API (`custom.metrics.k8s.io`) and External Metrics API (`external.metrics.k8s.io`).

[Key Aspects of EKS Metrics](#key-aspects-of-eks-metrics), the EKS-specific deep-dive, covers the **Metrics API** and **Custom Metrics API** as they apply in an EKS context, including the autoscaling components that consume them (HPA, VPA). It then moves to the **Kubernetes Control Plane Metrics**, which are specific to EKS's managed control plane and how they are exposed in Prometheus format via the `/metrics` endpoint. After this, it discusses **metrics-server**, including its role, its scrape interval behavior, and the important operational note that it is not installed by default on EKS.

Following this, we get to more EKS-specific components/architectures:

- **AWS Distro for OpenTelemetry (ADOT)** <br> *The AWS-recommended collection agent for EKS* <br> Here, we cover:
    - ADOT Collector pipeline architecture
    - How it is deployed as a managed EKS add-on
    - How it forwards metrics to Amazon Managed Service for Prometheus
- **CloudWatch Container Insights** <Br> *The AWS-native observability layer*
    - Aggregates metrics from EKS clusters into CloudWatch
    - Using Embedded Metric Format
    - With pre-built dashboards at the following levels:
        - Cluster
        - Node
        - Namespace
        - Service
        - Pod
- **Metrics on EKS Fargate** (NOT COVERED, as I consider it out of scope)

---

The sections are ordered from general to specific: Kubernetes fundamentals first, then EKS-specific tooling. If unfamiliar with Kubernetes metrics, please read sequentially; if looking for a specific EKS tool, you can jump directly to the relevant subsection under [Key Aspects of EKS Metrics](#key-aspects-of-eks-metrics).

# Resource Metrics Pipeline in Kubernetes
## About
This pipeline provides a limited set of metrics related to cluster components such as the `HorizontalPodAutoscaler` controller, as well as the `kubectl top` utility. These metrics are collected by the lightweight, short-term, in-memory metrics-server (see: [metrics-server (*implements* Metrics API)](#metrics-server-implements-metrics-api)) and are exposed via the `metrics.k8s.io` API (i.e. the Metrics API; see [Metrics API](#metrics-api)).

> **Reference**: [*Tools for Monitoring Resources*, **kubernetes.io/docs/tasks/debug/debug-cluster**](https://kubernetes.io/docs/tasks/debug/debug-cluster/resource-usage-monitoring/)

## Statistics collection methods
### Method 1: metrics-server
metrics-server (see: [metrics-server (*implements* Metrics API), in this document](#metrics-server-implements-metrics-api)) discovers all nodes on the cluster and queries each node's kubelet for CPU and memory usage. Each node's kubelet acts as a bridge between the Kubernetes master and the node, managing the pods and containers running on a machine. The kubelet translates each pod into its constituent containers and fetches individual container usage statistics from the container runtime through the container runtime interface.

### Method 2: cAdvisor
> About cAdvisor: [cAdvisor (Container Advisor), *MASTR*, **Untitled**](../untitled/miscellaneous-alphabetically-sorted-technical-references.md#cadvisor-container-advisor)

If you use a container runtime that uses Linux cgroups (see: [cgroup (Control Group), *MASTR*, **Untitled**](../untitled/miscellaneous-alphabetically-sorted-technical-references.md#cgroup-control-group)) and namespaces to implement containers, and the container runtime does not publish usage statistics, then the node's kubelet can look up those statistics directly using code from cAdvisor.

> **NOTE**: cAdvisor is not a separate deployment in modern Kubernetes - it is embedded directly into the kubelet binary and runs on every node automatically. No separate installation is required. It exposes container resource metrics via the kubelet's `/metrics/cadvisor` endpoint in Prometheus format.*

> **Reference**:
>
> - [*Resource metrics pipeline*, **kubernetes.io/docs/tasks/debug**](https://kubernetes.io/docs/tasks/debug/debug-cluster/resource-metrics-pipeline/)
> - [*Docker Monitoring with cAdvisor: The Definitive Guide*, **dash0.com**](https://www.dash0.com/guides/cadvisor-docker-monitoring)
> - [*cAdvisor and Kubernetes Monitoring Guide*, **cloudforecast.io**](https://www.cloudforecast.io/blog/cadvisor-and-kubernetes-monitoring-guide/)

## Statistics Aggregation via Metrics API

**NOTE**: *Resource Metrics API = Metrics API (see: [Metrics API, in this document](#metrics-api))*

> **Reference**: [`kubernetes`/`metrics`, **github.com**](https://github.com/kubernetes/metrics)
>
> - The above contains the line (under "Resource Metrics API"): <br> The API is implemented by metrics-server and prometheus-adapter.
> - metrics-server is known to implement Metrics API; see: <br> [metrics-server (*implements* Metrics API), in this document](#metrics-server-implements-metrics-api)
> - Hence, we see that Resource Metrics API refers to Metrics API

No matter how those statistics arrive, the kubelet then exposes the aggregated pod resource usage statistics through the metrics-server Metrics API. This API is served at the `/metrics/resource` endpoint on the kubelet's authenticated and read-only ports.

**SIDE NOTE**: *kube-apiserver's `/metrics` endpoint exposes point-in-time information; see: [Kubernetes Control Plane Metrics, in this document](#kubernetes-control-plane-metrics).*

> **Reference**: [*Tools for Monitoring Resources*, **kubernetes.io/docs/tasks/debug/debug-cluster**](https://kubernetes.io/docs/tasks/debug/debug-cluster/resource-usage-monitoring/)

## Architecture

The architecture components consist of the following:

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>cAdvisor</td>
      <td>
        <ul>
          <li>Daemon for...
            <ul>
              <li>collecting</li>
              <li>aggregating</li>
              <li>exposing</li>
            </ul>
            ... container metrics included in Kubelet.
          </li>
          <li>Embedded directly in the kubelet binary in modern Kubernetes <br> =&gt; <i>No separate installation required</i></li>
          <li>Metrics are accessible via the kubelet's <code>/metrics/cadvisor</code> endpoint</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td>kubelet</td>
      <td>
        <ul>
          <li>Node agent for managing container resources</li>
          <li>Resource metrics are accessible using the endpoints:
            <ul>
              <li><code>/metrics/resource</code></li>
              <li><code>/stats</code></li>
            </ul>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td>node-level resource metrics</td>
      <td>
        <ul>
          <li>API provided by the kubelet</li>
          <li>For discovering + retrieving per-node summarized stats</li>
          <li>These stats are available through the <code>/metrics/resource</code> endpoint</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td>metrics-server</td>
      <td>
        <ul>
          <li>Cluster add-on component that collects + aggregates resource metrics</li>
          <li>These resource metrics are pulled from each kubelet</li>
          <li>API server serves Metrics API for use by:
            <ul>
              <li>HPA (HorizontalPodAutoscaler)</li>
              <li>VPA (VerticalPodAutoscaler)</li>
              <li><code>kubectl top</code> command</li>
            </ul>
          </li>
          <li>metrics-server is a reference implementation of the Metrics API</li>
          <li>Default scrape interval is 60 seconds
            <ul>
              <li>Configurable via <code>--metric-resolution</code> flag</li>
              <li>The resolution at which kubelet calculates metrics is 15s</li>
              <li>Hence, values below 15s are not recommended</li>
            </ul>
          </li>
        </ul>
      </td>
    </tr>
    <tr>
      <td>Metrics API</td>
      <td>
        <ul>
          <li>Kubernetes API supporting access to...
            <ul>
              <li>CPU</li>
              <li>memory used</li>
            </ul>
            ... for workload autoscaling
          </li>
          <li>To make this work in your cluster, you need...
            <ul>
              <li>an API extension server</li>
              <li>that provides the Metrics API</li>
            </ul>
          </li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>

**NOTE**: *cAdvisor supports reading metrics from cgroups (see [cgroup (Control Group), *MASTR*, **Untitled**](../untitled/miscellaneous-alphabetically-sorted-technical-references.md#cgroup-control-group)), which works with typical container runtimes on Linux. If you use a container runtime that uses another resource isolation mechanism (e.g. virtualization), then that container runtime must support CRI Container Metrics (see: [CRI (Container Runtime Interface) Container Metrics, *MASTR*, **Untitled**](../untitled/miscellaneous-alphabetically-sorted-technical-references.md#cri-container-runtime-interface-container-metrics)) in order for metrics to be available to the kubelet.*

> **Reference**:
>
> - [*Resource metrics pipeline*, **kubernetes.io/docs/tasks/debug**](https://kubernetes.io/docs/tasks/debug/debug-cluster/resource-metrics-pipeline/)
> - [`metrics-server` FAQ, **github.com/kubernetes-sigs/metrics-server**](https://github.com/kubernetes-sigs/metrics-server/blob/master/FAQ.md)

# Full Metrics Pipeline in Kubernetes

***A.k.a. Custom Metrics Pipeline***

## About

A full metrics pipeline gives you access to richer metrics (as compared to resource metrics pipeline; see: [Resource Metrics Pipeline in Kubernetes, in this document](#resource-metrics-pipeline-in-kubernetes)). Kubernetes can respond to these metrics by automatically scaling or adapting the cluster based on its current state, using mechanisms such as the `HorizontalPodAutoscaler`. The monitoring pipeline fetches metrics from the kubelet and then exposes them to Kubernetes via an adapter by implementing either the `custom.metrics.k8s.io` or `external.metrics.k8s.io` API.

## Scope & Approach

When designing and implementing a full metrics pipeline you can make that monitoring data available back to Kubernetes. E.g. a `HorizontalPodAutoscaler` can use the processed metrics to work out how many pods to run for a component of your workload. Integration of a full metrics pipeline into a Kubernetes implementation is outside the scope of Kubernetes documentation because of the very wide scope of possible solutions (for reference, see: [CNCF Landscape, "Observability and Analysis - Monitoring" projects, **landscape.cncf.io**](https://landscape.cncf.io/?group=projects-and-products&view-mode=card#observability-and-analysis--monitoring); includes a mix of open-source software, paid-for software-as-a-service, and other commercial products).

As can be seen in this reference, there are a number of monitoring projects that can work with Kubernetes by scraping metric data and using that to help us observe our cluster. It is up to us to select the tool or tools that suit our needs. The choice of monitoring platform depends heavily on your needs, budget, and technical resources. Kubernetes does not recommend any specific metrics pipeline; many options are available (again, see the previous reference for CNCF landscape for observability and analysis).

> **Reference**: [*Tools for Monitoring Resources*, **kubernetes.io/docs/tasks/debug/debug-cluster**](https://kubernetes.io/docs/tasks/debug/debug-cluster/resource-usage-monitoring/)

# Key Aspects of EKS Metrics

May/may not be generalizable to Kubernetes, but is specifically for EKS:

- General Kubernetes components/aspects shall be indicated as such
- Other components/aspects are to be considered EKS-specific

## Metrics API

***General Kubernetes component***

> Part of the [Resource Metrics Pipeline in Kubernetes](#resource-metrics-pipeline-in-kubernetes).

Kubernetes provides the Metrics API, which allows access resource usage metrics (e.g. CPU and memory usage for nodes and pods), but the API only provides point-in-time information (i.e. data representing a specific "snapshot" of metrics as they existed at a precise date or time) and not historical metrics (i.e. aggregated metrics, running averages, etc.).

> **Reference**: [*Metrics for Amazon EKS and Kubernetes*, **docs.aws.amazon.com/prescriptive-guidance/latest**](https://docs.aws.amazon.com/prescriptive-guidance/latest/implementing-logging-monitoring-cloudwatch/kubernetes-eks-metrics.html)

---

The Metrics API offers a basic set of metrics to support automatic scaling and similar use cases (specifically, the `HorizontalPodAutoscaler` (HPA) and `VerticalPodAutoscaler` (VPA) use data from the Metrics API to adjust workload replicas and resources to meet customer demand). As stated before, this API makes information available about resource usage for node and pod, e.g. metrics for CPU and memory. ***In fact, its primary role is to feed resource usage metrics to Kubernetes autoscaler components.*** If you deploy the Metrics API into your cluster, clients of the Kubernetes API can then query for this information, and you can use Kubernetes' access control mechanisms to manage permissions for which clients can perform such queries.

> *You can also access Metrics API using `kubectl top`.*

**NOTE**: *The Metrics API only offers the minimum CPU and memory metrics to enable automatic scaling using HPA and/or VPA. If you would like to provide a more complete set of metrics, you can complement the simpler Metrics API by deploying a second metrics pipeline that uses the Custom Metrics API (for more on this, see the section [Custom Metrics API](#custom-metrics-api) in this document).*

> **Reference**:
>
> - [*Resource metrics pipeline*, **kubernetes.io/docs/tasks/debug**](https://kubernetes.io/docs/tasks/debug/debug-cluster/resource-metrics-pipeline/)
> - [metrics-server, **kubernetes-sigs.github.io**](https://kubernetes-sigs.github.io/metrics-server/)

## Custom Metrics API

***General Kubernetes component***

> Part of the [Full Metrics Pipeline in Kubernetes](#full-metrics-pipeline-in-kubernetes).

When the standard Metrics API (`metrics.k8s.io`) (see: [Metrics API, in this document](#metrics-api) and [Resource Metrics Pipeline in Kubernetes, in this document](#resource-metrics-pipeline-in-kubernetes)) does not provide sufficient signal for autoscaling decisions, Kubernetes exposes two additional APIs:

| API | Purpose |
| --- | --- |
| `custom.metrics.k8s.io` | Metrics tied to specific Kubernetes objects (e.g. HTTP requests per pod, queue depth per deployment). Use when the scaling signal originates *inside* the cluster and is associated with a particular resource. |
| `external.metrics.k8s.io` | Metrics that originate *outside* the cluster and are not tied to a Kubernetes object (e.g. message count in an AWS SQS queue, a third-party API response time). Use when the signal influencing scale decisions lives outside Kubernetes. |

> The above are also referenced in [Full Metrics Pipeline in Kubernetes, in this document](#full-metrics-pipeline-in-kubernetes)

Neither API has a built-in implementation; each requires a **metrics adapter** (see: [Kubernetes Metrics Adapters, *MASTR*, **Untitled**](../untitled/miscellaneous-alphabetically-sorted-technical-references.md#kubernetes-metrics-adapters)) to be deployed alongside a monitoring backend. The adapter translates monitoring-backend query results into the Kubernetes metrics API format. A commonly used adapter is the **Prometheus Adapter** (`prometheus-community/prometheus-adapter`), which can serve both `custom.metrics.k8s.io` and `external.metrics.k8s.io` by mapping PromQL queries to Kubernetes metric names.

> The `HorizontalPodAutoscaling` can then reference these APIs directly in its manifest. At runtime, the `HorizontalPodAutoscaling` evaluates all configured metrics and scales to the replica count required by whichever metric demands the highest value.

---

You can verify that an adapter is registered and query available metrics:

```bash
# Confirm the adapter is registered
kubectl get apiservices | grep custom

# List all available custom metrics
kubectl get --raw "/apis/custom.metrics.k8s.io/v1beta1" | jq .
```

> **Reference**: [*How to Fix HPA Not Fetching Custom Metrics*, **oneuptime.com/blog**](https://oneuptime.com/blog/post/2025-12-17-fix-hpa-not-fetching-custom-metrics/view)

---

> **Reference**:
>
> - [*Scaling an application*, **kubernetes.io/docs/tasks/run-application**](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#scaling-on-custom-metrics)
> - [*How to use Custom & External Metrics for Kubernetes HPA*, **livewyer.io**](https://livewyer.io/blog/how-to-use-custom-external-metrics-for-kubernetes-hpa/)
> - [`kubernetes`/`metrics`, **github.com**](https://github.com/kubernetes/metrics)

## Kubernetes Control Plane Metrics

> About Kubernetes Control Plane: [Kubernetes Control Plane, *MASTR*, **Untitled**](../untitled/miscellaneous-alphabetically-sorted-technical-references.md#kubernetes-control-plane)

Amazon EKS exposes control plane metrics through the Kubernetes API server (kube-apiserver; search within: [Kubernetes Control Plane, *MASTR*, **Untitled**](../untitled/miscellaneous-alphabetically-sorted-technical-references.md#kubernetes-control-plane)) in a Prometheus format by using the `/metrics` HTTP API endpoint. Note that CloudWatch can capture and ingest these metrics (from the same HTTP API endpoint, naturally). CloudWatch and CloudWatch Container Insights (also called AWS Container Insights) can also be configured to provide comprehensive metrics capture, analysis and alarming for your Amazon EKS nodes and pods.

If using Prometheus:

- Install Prometheus in your Kubernetes cluster
- This also enables graphing and viewing these metrics with a web browser

> **Reference**: [*Metrics for Amazon EKS and Kubernetes*, **docs.aws.amazon.com/prescriptive-guidance/latest**](https://docs.aws.amazon.com/prescriptive-guidance/latest/implementing-logging-monitoring-cloudwatch/kubernetes-eks-metrics.html)

## metrics-server (*implements* Metrics API)

***General Kubernetes component***

> Part of the [Resource Metrics Pipeline in Kubernetes](#resource-metrics-pipeline-in-kubernetes).

***The metrics-server implements the Metrics API.***

> **Reference**: [*Tools for Monitoring Resources*, **kubernetes.io/docs/tasks/debug**](https://kubernetes.io/docs/tasks/debug/debug-cluster/resource-usage-monitoring/)

### Use in EKS

The Kubernetes metrics-server is typically used for Amazon EKS and Kubernetes deployments to aggregate metrics, provide short-term historical information on metrics, and support features such as `HorizontalPodAutoscaler`. **Crucially**, metrics-server is meant only for autoscaling purposes. ***Do not use it to forward metrics to monitoring solutions, or as a source of monitoring solution metrics. In such cases please collect metrics from Kubelet `/metrics/resource` endpoint directly.***

> **Reference**:
> 
> - [*Metrics for Amazon EKS and Kubernetes*, **docs.aws.amazon.com/prescriptive-guidance/latest**](https://docs.aws.amazon.com/prescriptive-guidance/latest/implementing-logging-monitoring-cloudwatch/kubernetes-eks-metrics.html)
> - [`kubernetes-sigs`/`metrics-server`, **github.com**](https://github.com/kubernetes-sigs/metrics-server)
> - [metrics-server, **kubernetes-sigs.github.io**](https://kubernetes-sigs.github.io/metrics-server/)

---

**NOTE: Scrape interval** (as similar point has been mentioned in [Architecture, Resource Metrics Pipeline in Kubernetes, in this document](#architecture)): *metrics-server scrapes kubelet metrics at a default interval of 60 seconds (adjustable via the `--metric-resolution` flag). Setting this below 15 seconds is not recommended, as that is the resolution at which the kubelet itself calculates metrics. Teams configuring `HorizontalPodAutoscaling` should account for this latency when setting scaling thresholds, as the `HorizontalPodAutoscaling` controller will not observe a metric change until the next scrape cycle completes.*

> **Reference**: [`metrics-server` FAQ, **github.com/kubernetes-sigs/metrics-server**](https://github.com/kubernetes-sigs/metrics-server/blob/master/FAQ.md)

### Not Installed in EKS by Default

- On Amazon EKS, metrics-server is **not installed by default**
- It can be deployed manually via the upstream manifest
- Or, it can be installed as a community EKS add-on <br> ... *through the AWS console or EKS APIs*

> **Reference**:
>
> - [*Resource metrics pipeline*, **kubernetes.io/docs/tasks/debug**](https://kubernetes.io/docs/tasks/debug/debug-cluster/resource-metrics-pipeline/)
> **Reference**: [*View resource usage with the Kubernetes Metrics Server*, **docs.aws.amazon.com/eks**](https://docs.aws.amazon.com/eks/latest/userguide/metrics-server.html)

## AWS Distro for OpenTelemetry (ADOT)

***EKS-specific component***

AWS Distro for OpenTelemetry (ADOT) is a secure, AWS-supported distribution of the upstream OpenTelemetry project. It is the AWS-recommended collection agent for metrics (and traces) on EKS. ADOT consists of SDKs, auto-instrumentation agents, a collector, and exporters. With ADOT, applications can be instrumented once and send correlated metrics and traces to multiple AWS and partner monitoring solutions.

> **Reference**: [AWS Distro for OpenTelemetry, **aws-otel.github.io**](https://aws-otel.github.io/)

### ADOT Collector

AWS Distro for OpenTelemetry Collector (ADOT Collector) is an AWS-supported version of the upstream OpenTelemetry Collector that is fully compatible with AWS computing platforms, including EKS. It enables users to send telemetry data to AWS managed services such as Amazon CloudWatch, Amazon Managed Service for Prometheus, and AWS X-Ray.

![](../_common-resources/adot-collector-architecture.png)

**NOTE**: *μService = Microservice*

> **Reference**: [*Metrics and traces collection using Amazon EKS add-ons for AWS Distro for OpenTelemetry*, **aws.amazon.com/blogs/containers**](https://aws.amazon.com/blogs/containers/metrics-and-traces-collection-using-amazon-eks-add-ons-for-aws-distro-for-opentelemetry/)

The ADOT Collector configuration uses the same configuration syntax/design from OpenTelemetry Collector. For more information regarding OpenTelemetry Collector configuration, see: [`*Configuration*, **opentelemetry.io/docs/collector**](https://opentelemetry.io/docs/collector/configuration/), so you can customize or port your OpenTelemetry Collector configuration files when running ADOT Collector. 

> **Reference**: [`aws-observability`/`aws-otel-collector`, **github.com**](https://github.com/aws-observability/aws-otel-collector)

---

The collector pipeline is structured as:

- **Receivers**: Pull or accept telemetry data <br> Receiver examples: *Prometheus Receiver for scraping `/metrics` endpoints*
- **Processors**: Optional transforms <br> Transform examples: *Filtering, renaming, aggregation, and rate conversion*
- **Exporters**: Send data to a backend <br> Exporter examples: *AWS CloudWatch EMF Exporter, Prometheus Remote Write Exporter*

**NOTE**: *The collector architecture allows multiple instances of such pipelines to be set up via a Kubernetes YAML manifest.*

> **Reference**: [*Metrics and traces collection using Amazon EKS add-ons for AWS Distro for OpenTelemetry*, **aws.amazon.com/blogs/containers**](https://aws.amazon.com/blogs/containers/metrics-and-traces-collection-using-amazon-eks-add-ons-for-aws-distro-for-opentelemetry/)

### ADOT as an EKS Add-on

Amazon EKS supports deploying ADOT as a managed EKS add-on. The ADOT add-on is an implementation of a Kubernetes Operator (see: [Kubernetes Operator, *MASTR*, **Untitled**](../untitled/miscellaneous-alphabetically-sorted-technical-references.md#kubernetes-operator)), which is a software extension to Kubernetes that makes use of custom resources to manage applications and their components. The add-on installs one component of ADOT, namely the ADOT Operator (an implementation of a Kubernetes Operator), which watches for a custom resource named `OpenTelemetryCollector` and manages the lifecycle of an ADOT Collector based on the configuration settings specified in the custom resource. The following figure shows an illustration of how this works.

![](../_common-resources/adot-collector-deployment.png)

When a custom OpenTelemetryCollector resource is deployed to an EKS cluster, this will trigger the ADOT add-on to provision an ADOT Collector that includes a traces and metrics pipelines with components, as shown in the first illustration above. The collector is launched into the `aws-otel-eks` namespace as a Kubernetes Deployment with the name `${custom-resource-name}-collector`. A ClusterIP service with the same name is launched as well.

> **Reference**:
>
> - [*Getting Started with AWS Distro for OpenTelemetry using EKS Add-Ons*, **aws-otel.github.io**](https://aws-otel.github.io/docs/getting-started/adot-eks-add-on/)
> - [*Metrics and traces collection using Amazon EKS add-ons for AWS Distro for OpenTelemetry*, **aws.amazon.com/blogs/containers**](https://aws.amazon.com/blogs/containers/metrics-and-traces-collection-using-amazon-eks-add-ons-for-aws-distro-for-opentelemetry/)

### Sending Metrics to Amazon Managed Service for Prometheus (AMP)

ADOT Collector can scrape Prometheus-instrumented endpoints (using the **Prometheus Receiver**) and forward metrics to AMP (or more precisely to our management portal workspace) (using the **Prometheus Remote Write Exporter**). HTTP requests to AMP are authenticated with AWS SigV4 via the Sigv4 Authentication Extension. The collector automatically discovers Prometheus metrics endpoints on Amazon EKS and uses the configuration found in `kubernetes_sd_config` (this is a service discovery (SD) configuration block name for Prometheus; `kubernetes_sd_configs` specifically is for discovering and scraping Kubernetes targets; source: [*Prometheus service discovery*, **docs.victoriametrics.com/victoriametrics**](https://docs.victoriametrics.com/victoriametrics/sd_configs/)).

**NOTE**: *In [ADOT Collector, in this document](#adot-collector), Prometheus Receiver is mentioned as an example for the receiver component of the ADOT collector pipeline, whereas Prometheus Remote Write Exporter is mentioned as an example for the exporter component. This is a valuable link, in order to relate these particular tools to the proper architecture of the collector pipeline.*

> **Reference**:
>
> - [*Set up metrics ingestion using AWS Distro for OpenTelemetry on an Amazon EKS cluster*, **docs.aws.amazon.com/prometheus**](https://docs.aws.amazon.com/prometheus/latest/userguide/AMP-onboard-ingest-metrics-OpenTelemetry.html)
> - [*Scraping metrics using AWS Distro for OpenTelemetry*, **eksworkshop.com**](https://www.eksworkshop.com/docs/observability/open-source-metrics/adot)

### Receivers in ADOT Collectors

A receiver is a component within the ADOT Collector that ingests, scrapes, or receives telemetry data-metrics, logs, or traces from various AWS services and applications. These components, such as the `awscontainerinsight` receiver or theZ `awscloudwatch` receiver, pull data from CloudWatch, EKS, ECS or S3, transforming it into a standardized format for analysis in tools like Amazon Managed Grafana or CloudWatch.

E.g.: *The AWS CloudWatch receiver is an OpenTelemetry Collector component that integrates with AWS CloudWatch to retrieve metrics from AWS services. It acts as a bridge, pulling CloudWatch metrics via the AWS CloudWatch API and converting them to OpenTelemetry format for export to your chosen observability backend.*

> **Reference**: [*How to Configure the AWS CloudWatch Receiver in the OpenTelemetry Collector*, **oneuptime.com/blog**](https://oneuptime.com/blog/post/2026-02-06-aws-cloudwatch-receiver-opentelemetry-collector/view)

## CloudWatch Container Insights (*shortened to "Container Insights"*)

***AWS-specific component***

CloudWatch Container Insights is a a fully managed monitoring and observability service that is used to collect, aggregate, and summarise metrics and logs from your containerised applications and microservices. Container Insights is available for Amazon Elastic Container Service (Amazon ECS), Amazon Elastic Kubernetes Service (Amazon EKS), RedHat OpenShift on AWS (ROSA), and Kubernetes platforms on Amazon EC2. Container Insights supports collecting metrics from clusters deployed on AWS Fargate for both Amazon ECS and Amazon EKS.

CloudWatch Container Insights has been supported by ECS Agent and CloudWatch Agent to collect infrastructure metrics for many resources such as such as CPU, memory, disk, and network. To migrate existing customers to use OpenTelemetry, AWS Container Insights Receiver (together with CloudWatch EMF Exporter) aims to support the same CloudWatch Container Insights experience for the following platforms:

- Amazon ECS
- Amazon EKS
- Kubernetes platforms on Amazon EC2

> **Reference**:
> 
> - [*Container Insights*, **docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring**](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ContainerInsights.html)
> - [*Request Access: Amazon CloudWatch Container Insights Beta for ECS and Fargate*, **pages.awscloud.com**](https://pages.awscloud.com/cloudwatch-containerinsights-beta.html)

---

Container Insights collects, aggregates, and summarises metrics and logs from containerised applications running on EKS. Metrics data is collected as **performance log events** using the **Embedded Metric Format (EMF)**, a structured JSON schema that allows high-cardinality data to be ingested and stored at scale. ***From this data, CloudWatch creates aggregated metrics at the cluster, node, pod, task, and service level.*** The metrics that Container Insights collects are available in CloudWatch automatic dashboards, and are also viewable in the *Metrics* section of the CloudWatch console. *Note that metrics are not visible until the container tasks have been running for some time.*

> **Reference**: [*Container Insights*, **docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring**](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ContainerInsights.html)

---

\[TO BE COVERED\]

Areas not covered here but worth exploring:

- Encryption in Container Insights metrics data transfer
- OpenTelemetry support in Container Insights

### Deployment

\[TO BE COVERED\]

> **Reference**:
>
> - [*Container Insights*, **docs.aws.amazon.com/AmazonCloudWatch**](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ContainerInsights.html)
> - [*Setting up Container Insights on Amazon EKS and Kubernetes*, **docs.aws.amazon.com/AmazonCloudWatch**](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/deploy-container-insights-EKS.html)
> - [*Amazon EKS and Kubernetes Container Insights with enhanced observability metrics*, **docs.aws.amazon.com/AmazonCloudWatch**](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-metrics-enhanced-EKS.html)

## Metrics on EKS Fargate

***EKS-specific component***

\[TO BE COVERED\]

*However, this may be out of scope for our use-case.*