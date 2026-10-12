---
type: article
layout: article
lang: en
slug: troubleshooting-eks-resource-contention
permalink: /entries/troubleshooting-eks-resource-contention/
title: "Investigating CPU Throttling and Memory Failures in Amazon EKS"
date: 2026-10-12
category: "Cloud & Platform Engineering"
read_time: 9
excerpt: "A practical troubleshooting approach to CPU throttling, memory-related container failures, and application latency in Amazon EKS, informed by a real team investigation."
---

## Introduction

A Kubernetes workload can remain running while its users experience slow responses. In other cases, a container may restart because it runs out of memory. These symptoms can appear together, but they do not have the same cause and should not be investigated as if they were interchangeable.

While working as a Platform/DevOps Engineer, I helped investigate resource-related performance issues affecting API and ingestion workloads in an Amazon Elastic Kubernetes Service (EKS) environment. The symptoms included increased application latency, CPU throttling, and memory-related container failures.

I used Prometheus and Grafana to review monitoring data, examined memory-related container status, and worked with the team during the investigation and resolution.

This article explains the incident context, the Kubernetes concepts involved, and a repeatable way to investigate similar problems. The troubleshooting commands and PromQL examples below are reference examples for future investigations; they are not presented as commands I necessarily ran during the original incident.

## 1. Understanding the symptoms

The affected environment included API and ingestion workloads with different responsibilities and resource-consumption patterns. The observed symptoms were:

- Increased application latency.
- CPU throttling.
- Memory-related container failures.

These signals indicate that the workloads need investigation, but they do not independently establish a root cause. For example, high latency can also be related to application code, downstream services, database performance, network behavior, or other dependencies.

The first objective is therefore to establish when the problem occurs, which workloads are affected, and whether application symptoms correlate with resource behavior.

## 2. Investigation with Prometheus and Grafana

I used Prometheus and Grafana to examine available monitoring data and relate workload resource behavior to the reported performance symptoms. For memory-related failures, container status information was also relevant to understanding whether a container had been terminated.

A useful investigation separates three questions:

1. **What is the user-visible impact?** Check latency, errors, throughput, and the time window in which the issue occurred.
2. **What is happening to the workload?** Review CPU behavior, memory usage, Pod restarts, and termination reasons.
3. **What evidence supports the current hypothesis?** Compare signals over the same time window and avoid treating correlation as proof of causation.

### CPU throttling

CPU throttling occurs when a container is restricted by its configured CPU limit. The container may continue running, but it can receive less CPU time than the workload needs at that moment. Under load, this can contribute to slower processing and increased latency.

CPU utilization and CPU throttling are related but different signals. A workload may show throttling even when a simple CPU-usage graph does not look consistently high. The available metrics, time range, and container configuration all matter when interpreting the data.

### Memory pressure and OOMKilled

Memory-related failures require a separate investigation. A container may be terminated after an out-of-memory condition; Kubernetes commonly reports the container termination reason as `OOMKilled`.

Useful evidence can include the container's last termination state, restart history, Pod events, memory usage, and its configured memory limit. An `OOMKilled` status is an important clue, but further investigation may still be needed to understand why memory use grew or why the configured limit was insufficient.

CPU throttling generally limits execution time; an out-of-memory termination stops the affected container. Distinguishing the two helps avoid applying the wrong remediation.

### Useful commands for a similar investigation

The following commands are examples of checks an engineer can use when investigating an affected workload. Run them with the appropriate namespace and Pod name.

```bash
kubectl get pods -n <namespace>
kubectl describe pod <pod-name> -n <namespace>
kubectl get events -n <namespace> --sort-by=.lastTimestamp
kubectl top pod -n <namespace>
kubectl top pod <pod-name> -n <namespace> --containers
```

The `describe` output can show container state, restart information, configured resources, and events. The `top` commands provide a current usage snapshot when the Kubernetes Metrics API is available; they do not replace historical monitoring data.

For an exited container, the following can help inspect its previous logs:

```bash
kubectl logs <pod-name> -n <namespace> --previous
```

The command is useful only when a previous container instance exists and its logs are available.

### Example PromQL queries

Metric names and labels can vary depending on the Prometheus setup and exporters. Validate the available metrics before using these examples.

CPU usage by Pod:

```promql
sum by (pod) (
  rate(container_cpu_usage_seconds_total{container!="", pod!=""}[5m])
)
```

Proportion of CPU CFS periods that were throttled, by Pod:

```promql
sum by (pod) (
  rate(container_cpu_cfs_throttled_periods_total{container!="", pod!=""}[5m])
)
/
sum by (pod) (
  rate(container_cpu_cfs_periods_total{container!="", pod!=""}[5m])
)
```

Memory working set by Pod:

```promql
sum by (pod) (
  container_memory_working_set_bytes{container!="", pod!=""}
)
```

These queries are starting points, not universal drop-in queries. Some metrics may be absent, labels may differ, and the query should be adapted to the cluster's monitoring configuration. A metric should always be interpreted alongside application behavior and the relevant container configuration.

## 3. Choosing the right remediation

The investigation and resolution of this incident were collaborative efforts. The team used the observed symptoms and monitoring data to guide troubleshooting around resource-related performance issues.

For any similar incident, the remediation should follow the evidence rather than assume that one Kubernetes setting is always responsible.

### Review resource configuration

CPU requests influence scheduling decisions, while CPU limits constrain CPU consumption and can lead to throttling. Memory requests influence scheduling, and memory limits constrain memory use; exceeding the applicable limit can result in container termination.

Changing requests or limits may be appropriate when the evidence supports it, but blindly increasing values can hide the underlying issue or leave the cluster without sufficient capacity. Resource settings should be reviewed in the context of observed usage, workload behavior, and available node capacity.

### Consider workload isolation

API and ingestion workloads can have different resource profiles. If evidence shows that workloads interfere with one another, separating their placement or otherwise improving isolation may be worth considering.

Kubernetes supports several scheduling and isolation mechanisms, but the correct choice depends on the cluster's actual configuration and operational requirements. Taints, tolerations, affinity rules, and other scheduling controls should not be added automatically just because CPU or memory pressure is present.

### Evaluate scaling carefully

Scaling can help when demand increases and the application can benefit from additional replicas. However, adding replicas also increases total resource demand. If nodes do not have enough capacity, new Pods may remain pending or fail to improve performance.

The Horizontal Pod Autoscaler (HPA) changes replica counts according to configured metrics and policies. It does not itself add worker-node capacity. Cluster-level scaling is a separate concern, and neither mechanism guarantees a fix if the bottleneck is in a database, external service, application code, or another dependency.

## 4. A repeatable troubleshooting workflow

For a similar incident, I would use the following workflow:

1. **Establish impact.** Identify affected services, the start time, latency or error changes, and whether the issue is ongoing.
2. **Correlate monitoring signals.** Review application performance alongside CPU, throttling, memory, and restart metrics over the same time window.
3. **Inspect Pod state.** Check container termination reasons, restart history, relevant events, and previous logs where available.
4. **Form a hypothesis.** Consider resource constraints, workload interference, scaling behavior, and non-resource bottlenecks.
5. **Validate before changing configuration.** Compare the hypothesis with the evidence and the actual workload configuration.
6. **Apply a targeted change collaboratively.** Choose a remediation that addresses the likely cause and is appropriate for the environment.
7. **Verify the result.** Compare application performance and resource behavior after the change. Check for unintended side effects, such as increased resource consumption or Pods that cannot be scheduled.

This workflow helps keep troubleshooting evidence-based and makes it easier to explain why a particular action was chosen.

## 5. Lessons learned

**Monitoring is most useful when connected to application impact.** A CPU or memory graph is only one part of the investigation; it becomes more meaningful when compared with latency, errors, and workload events.

**CPU throttling and OOMKilled are different signals.** Throttling limits CPU execution, while an out-of-memory termination stops a container. Each requires its own evidence.

**Scaling is not a universal solution.** More replicas can help some workloads but cannot automatically fix a dependency bottleneck or a lack of cluster capacity.

**Kubernetes configuration should follow evidence.** Requests, limits, scaling policies, and scheduling rules are tools to solve specific problems, not a checklist to apply to every incident.

**Incident resolution is collaborative.** Sharing observations, testing hypotheses, and validating the outcome with the team helps avoid premature conclusions and unsupported assumptions.

## Conclusion

Investigating resource-related problems in Amazon EKS requires more than noticing high CPU usage or a restarted container. Engineers need to connect application symptoms with resource metrics, distinguish CPU throttling from memory-related failures, and select corrective actions based on the evidence.

My experience investigating API and ingestion workload issues with Prometheus and Grafana reinforced the importance of structured troubleshooting and collaborative incident resolution. The goal is not simply to restore a workload, but to understand the observed failure, make a justified change, and validate the application's behavior afterward.

## Interview questions to practise

1. What is the difference between CPU throttling and `OOMKilled`?
2. How would you investigate increased latency in an EKS application?
3. What is the difference between CPU requests and CPU limits?
4. What evidence would you collect after a container is OOMKilled?
5. How does the Horizontal Pod Autoscaler work, and what does it not do?
6. Why might adding more replicas fail to solve a performance issue?
7. How would you distinguish resource contention from a database or network bottleneck?
8. How would you validate that a remediation was successful?

When answering, explain the evidence you examined, the reasoning behind your decisions, the work you personally contributed, and what you learned. Avoid claiming a specific command, configuration, metric, or result unless you can support it with your own experience.
