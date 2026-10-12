---
type: article
layout: article
lang: en
slug: troubleshooting-eks-resource-contention
permalink: /entries/troubleshooting-eks-resource-contention/
title: "When Kubernetes Pods Are Running but the Application Is Slow"
date: 2026-10-12
category: "Cloud & Platform Engineering"
read_time: 9
excerpt: "A practical troubleshooting story from an Amazon EKS environment: investigating API and ingestion latency alongside CPU throttling and memory-related container failures."
---

A Pod can be `Running` and an application can still be unhealthy.

I was reminded of this while working on an Amazon EKS platform where API and ingestion workloads experienced performance issues. The symptoms included increased latency, CPU throttling, and memory-related container failures. Prometheus and Grafana were part of the investigation, and resolving the issue was a collaborative effort with the team.

What made this kind of incident interesting was that the symptoms pointed toward resource pressure, but they did not automatically identify one root cause. CPU throttling and memory exhaustion are different problems. They can affect the same application at the same time, but they need different evidence and different responses.

This article describes how I think about that class of incident, what I look for in the evidence, and how I would structure a repeatable investigation. The commands and queries below are examples for a similar environment; they are not presented as a record of the exact commands used during the original incident.

## Start with the impact, not the first red graph

When latency rises, it is tempting to open a CPU dashboard, see a high value, and immediately increase the CPU limit. That might help in some cases, but it is not a diagnosis.

I start by framing the problem:

- Which workload is affected: API, ingestion, or both?
- When did the symptoms begin?
- Did latency increase at the same time as CPU throttling, memory pressure, or restarts?
- Is the issue continuous, or does it happen during traffic spikes?
- Did anything change around that time, such as application releases, traffic volume, or infrastructure configuration?

The aim is to build a timeline and narrow down possible explanations. A graph is a clue; the relationship between several signals is more useful than any one metric in isolation.

## The investigation: connect application symptoms to resource behavior

Prometheus and Grafana helped us inspect monitoring data and understand the resource behavior around the affected workloads. For a similar incident, I would organize the investigation around three questions.

### 1. Is CPU throttling contributing to latency?

CPU throttling happens when a container is prevented from using more CPU time because of its configured CPU limit. The container may remain healthy from Kubernetes' point of view while requests take longer to complete.

That distinction matters: CPU utilization alone does not tell the whole story. A container can be throttled even when a dashboard does not show the node at 100% CPU, because the limit applies to the container's CPU quota.

A Prometheus metric commonly used to investigate this is `container_cpu_cfs_throttled_periods_total`, considered alongside `container_cpu_cfs_periods_total`. The exact metric availability and labels depend on the monitoring stack and exporters.

An illustrative PromQL query for a throttling ratio is:

```promql
sum by (namespace, pod, container) (
  rate(container_cpu_cfs_throttled_periods_total[5m])
)
/
sum by (namespace, pod, container) (
  rate(container_cpu_cfs_periods_total[5m])
)
```

Treat this as a starting point, not a universal dashboard query. Metric names, labels, and recording rules vary between environments, and the result should be checked against the actual monitoring setup.

The next step is to compare the time range with application latency and workload activity. If throttling rises when latency rises, that is useful evidence. It still does not prove that throttling is the only cause: application behavior, downstream dependencies, and other bottlenecks may also contribute.

### 2. Is memory pressure causing container failures?

Memory-related failures need a different line of investigation. If a container is terminated with the reason `OOMKilled`, Kubernetes is reporting an out-of-memory termination. That is not the same as CPU throttling.

For a live or recently restarted Pod, useful checks include:

```bash
kubectl get pods -n <namespace>
kubectl describe pod <pod-name> -n <namespace>
kubectl get pod <pod-name> -n <namespace> -o jsonpath='{.status.containerStatuses[*].lastState.terminated.reason}'
```

These commands are generic examples. The exact Pod name, namespace, and available status fields depend on the workload and whether the container has restarted.

I would then compare the termination evidence with memory usage, the configured memory limit, restart timing, and relevant events. A single memory graph may miss a short-lived spike, so it is important to examine an appropriate time window and correlate the data with restarts.

An `OOMKilled` status is strong evidence of an out-of-memory termination, but it does not by itself explain why memory grew. The next question is whether the pattern comes from expected workload demand, an unsuitable limit, application behavior, or another contributing factor.

### 3. Are the workloads competing for resources?

API and ingestion services can have different resource profiles. An ingestion workload may experience bursts while processing incoming data; an API workload may be more sensitive to response-time variation. When workloads share nodes, their resource demands can interact.

That does not mean shared placement is automatically wrong, or that moving Pods is always the answer. The useful question is whether the observed resource behavior and workload placement support a contention hypothesis.

I would look at the affected Pods, their nodes, the timing of the symptoms, and available node and workload metrics. I would also check whether the cluster has enough capacity to accommodate the current workload and any planned scaling.

## A simple way to organize the evidence

The following is a troubleshooting guide for similar incidents, rather than a claim that every signal below was collected during this particular event.

| Observation | What it may indicate | What to check next |
|---|---|---|
| Latency rises with CPU throttling | CPU limits may be constraining processing | Compare throttling, latency, traffic, and CPU configuration |
| A container reports `OOMKilled` | An out-of-memory termination occurred | Review memory usage, limits, restart timing, and events |
| Restarts increase during traffic spikes | Demand may be exposing a resource or application problem | Correlate traffic, memory, CPU, and restart timelines |
| More replicas do not improve latency | The bottleneck may be elsewhere or capacity may be insufficient | Check downstream dependencies, node capacity, and per-Pod behavior |

The key is to move from observation to hypothesis, then look for evidence that supports or weakens it. This prevents a common operational mistake: applying a familiar Kubernetes fix before establishing what problem it is meant to solve.

## Resolution is a set of decisions, not a single magic setting

In the EKS incident, investigation and resolution involved working with the team to address the resource-related performance problems. The experience reinforced the importance of considering workload behavior, isolation, and scaling together.

For a similar incident, the corrective action should follow the evidence:

- **If CPU throttling is confirmed as a contributor**, review the workload's CPU demand and configured limit, and validate any change against latency and resource metrics.
- **If memory exhaustion is confirmed**, examine memory behavior and the configured limit before deciding whether to change capacity or investigate the application further.
- **If workload contention is supported by the evidence**, consider whether workload separation or placement changes would reduce interference.
- **If demand exceeds available capacity**, evaluate scaling at the appropriate level and confirm that the cluster can schedule the resulting Pods.

These actions are not interchangeable. Increasing a CPU limit does not solve a memory failure. Raising a memory limit does not fix CPU throttling. Adding replicas does not necessarily help when the bottleneck is a database, network, or another shared dependency.

Kubernetes features such as resource requests and limits, Horizontal Pod Autoscaler, and node placement controls can be relevant, but they should be described as incident actions only when the actual configuration changes are known. I would rather document a verified decision accurately than list advanced features simply to make a case study sound more impressive.

## Validate the change against the original symptom

A successful deployment or a Pod returning to `Running` is not enough to prove that the incident is resolved.

After a remediation, I would compare the same signals used during investigation:

1. Did application latency return to an acceptable level?
2. Did CPU throttling change?
3. Did memory-related terminations or restarts stop?
4. Did the change introduce a new problem, such as insufficient cluster capacity or unnecessary resource consumption?
5. Does the improvement persist under representative workload conditions?

Where reliable before-and-after data is available, it can support a strong engineering result. If it is not available, it is better to describe the observed outcome qualitatively than to invent a percentage or claim a measured improvement.

## What I took from the experience

The most useful lesson was not a particular Kubernetes setting. It was the value of investigating the relationship between application symptoms and resource behavior.

CPU throttling and memory-related termination are different signals. Monitoring makes them visible, but engineers still need to interpret them in context. Workload separation and scaling can be useful, yet neither should be applied automatically without considering the evidence and the wider system.

Troubleshooting also benefits from collaboration. Sharing the timeline, comparing observations, and validating a proposed change helps the team avoid jumping to conclusions.

For platform engineers, the goal is not merely to get a Pod running again. It is to understand what affected the application, choose a proportionate response, and verify the outcome.

## Further reading

- [Kubernetes: Resource Management for Pods and Containers](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/)
- [Kubernetes: Horizontal Pod Autoscaling](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)
- [Amazon EKS documentation](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html)
- [Prometheus documentation](https://prometheus.io/docs/introduction/overview/)

---

*This article describes a real class of resource-related issues encountered in an EKS environment. Diagnostic commands and PromQL examples are included as reusable learning examples; they should not be read as a claim that these exact commands or queries were used during the original incident.*
