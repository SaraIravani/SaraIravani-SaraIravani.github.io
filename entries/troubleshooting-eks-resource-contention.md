---
type: article
layout: article
lang: en
slug: troubleshooting-eks-resource-contention
permalink: /entries/troubleshooting-eks-resource-contention/
title: "When Kubernetes Workloads Slow Down: Investigating CPU and Memory Issues in EKS"
date: 2026-10-12
category: "Cloud & Platform Engineering"
read_time: 8
excerpt: "How I approached an EKS performance incident involving API and ingestion workloads, using Prometheus, Grafana, and evidence-led troubleshooting."
---

## When a workload is running, but the application is struggling

A Kubernetes Pod can be in the `Running` state while the application inside it is having a bad day. Requests may take longer than usual, CPU time may be constrained, or a container may restart after running out of memory. Seeing these symptoms together can be confusing because they are related to resources, but they do not necessarily share one cause.

I ran into this kind of challenge while working as a Platform/DevOps Engineer on an AWS EKS environment. API and ingestion workloads were experiencing performance issues, including increased latency, CPU throttling, and memory-related container failures.

My role in the investigation involved reviewing monitoring data in Prometheus and Grafana, examining memory-related container status, and working with the team to investigate and address the issue. I am describing the technical approach here rather than presenting unverified configuration changes or claiming that one metric alone proved the root cause.

The most useful lesson was simple: **start with the symptoms, follow the evidence, and do not confuse a plausible explanation with a confirmed root cause.**

## Start with the impact, not the first alarming graph

When an application becomes slow, it is tempting to look at a CPU graph and immediately conclude that the cluster needs more CPU. That might be the right direction, but it is only a hypothesis.

I started by looking at the available monitoring data and connecting resource behavior with the application symptoms. For a similar incident, I would want to answer three questions:

- What changed from the application's point of view? For example, did latency increase, did errors rise, or did the problem affect only one workload?
- What was happening to the affected containers around the same time?
- What evidence would distinguish resource pressure from a database, network, or application-level bottleneck?

That last question matters. High latency can coincide with CPU pressure without being caused exclusively by it. Comparing signals over the same time window makes the investigation more useful than reading individual charts in isolation.

## Two symptoms that need different investigations

### CPU throttling: the container is running, but it may be constrained

CPU throttling can occur when a container reaches its configured CPU limit. Kubernetes does not necessarily restart the container in this situation. Instead, the container can be restricted from using more CPU time during the relevant scheduling periods.

For a request-serving API, that restriction can contribute to slower processing. However, throttling is evidence to investigate, not proof that changing a CPU limit is automatically the correct fix.

For a similar incident, I would compare the throttling signal with application latency, CPU usage, workload demand, and the container's actual resource configuration. I would also check whether the timing matches: a metric that looks concerning outside the incident window may not explain the incident itself.

### Memory failures: understand why the container stopped

Memory-related failures need a different line of investigation. A container that is killed because of an out-of-memory condition may show the termination reason `OOMKilled`.

That status tells an engineer something important about how the container ended, but it does not by itself explain why memory use reached that point. The next questions are about the container's previous state, restart history, relevant events, memory usage, and configured memory limit.

CPU throttling generally limits execution time; an out-of-memory termination stops the affected container. Treating these as the same problem can lead to the wrong remediation.

## Tools and practical checks

Prometheus and Grafana were part of my investigation. They helped me review the available monitoring data and relate resource behavior to the symptoms being reported.

For a future investigation, I would also use Kubernetes state and events to add context to the graphs. The following commands are practical reference examples; they are not a claim that I ran every command during the original incident. Replace the placeholders with the correct namespace and Pod name.

```bash
kubectl get pods -n <namespace>
kubectl describe pod <pod-name> -n <namespace>
kubectl get events -n <namespace> --sort-by=.lastTimestamp
kubectl top pod -n <namespace>
kubectl top pod <pod-name> -n <namespace> --containers
```

The `describe` output can show container states, restart information, configured resources, and events. The `top` commands show a current usage snapshot when the Metrics API is available; they do not replace historical metrics.

If a previous container instance exists and its logs are available, this command can help inspect them:

```bash
kubectl logs <pod-name> -n <namespace> --previous
```

### Example PromQL queries

These queries are starting points for a comparable investigation, not screenshots or measurements from the incident. Metric availability and labels depend on the Prometheus setup, so validate them in the target environment before relying on the results.

CPU usage by Pod:

```promql
sum by (pod) (
  rate(container_cpu_usage_seconds_total{container!="", pod!=""}[5m])
)
```

Approximate proportion of CPU CFS periods that were throttled, by Pod:

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

A query is only useful if the metric exists, its labels match the query, and the result is interpreted in context. A graph is evidence to investigate—not a conclusion on its own.

## From investigation to remediation

The investigation and resolution were collaborative efforts with the team. The observations from monitoring and container status informed our troubleshooting. I do not want to attribute a specific Kubernetes setting to this incident without being certain that it was the setting we changed.

That distinction is important because several different actions can be appropriate depending on what the evidence shows.

**If CPU throttling is supported by the evidence**, review the container's CPU configuration and the workload's demand before deciding whether a resource change is justified. CPU requests influence scheduling; CPU limits constrain CPU consumption and can cause throttling.

**If a container is OOMKilled**, review its termination details, memory behavior, and configured memory limit. Increasing the limit without understanding the memory pattern can simply postpone another failure or leave less capacity for other workloads.

**If workloads are interfering with one another**, workload isolation or placement changes may be worth considering. API and ingestion workloads can have different resource profiles, but scheduling controls should match the actual cluster design rather than be added automatically.

**If demand is changing**, scaling may help—but only when the application can benefit from more replicas and the cluster has enough capacity. The Horizontal Pod Autoscaler changes Pod replica counts according to configured metrics and policies; it does not itself add worker-node capacity. Scaling also will not fix a bottleneck that actually sits in a database, external service, network path, or application code.

The decision should follow the evidence. There is no single Kubernetes setting that resolves every incident involving latency, CPU, or memory.

## A troubleshooting sequence I would use again

For a similar incident, I would work through the following sequence with the team:

1. **Define the impact.** Identify affected workloads and establish when latency, errors, or restarts changed.
2. **Correlate the signals.** Review application performance alongside CPU, throttling, memory, and restart metrics over the same time window.
3. **Inspect the container state.** Check termination reasons, restart history, events, and previous logs where available.
4. **Test the hypothesis.** Consider resource limits and demand, workload interference, scaling behavior, and non-resource bottlenecks.
5. **Make a targeted change.** Choose a remediation that fits the evidence and the environment, rather than changing several settings without a clear reason.
6. **Validate after the change.** Check whether application behavior improved and whether the change introduced side effects, such as increased resource demand or Pods that cannot be scheduled.

This sequence is not meant to make troubleshooting mechanical. It helps keep the investigation structured while leaving room for the evidence to change the initial hypothesis.

## What I took away from the incident

The incident reinforced a few habits I want to carry into every platform investigation.

First, **monitoring is most valuable when it is connected to application impact**. CPU and memory graphs are useful, but they become more meaningful when considered alongside latency, errors, and container events.

Second, **similar-looking symptoms can require different fixes**. CPU throttling and an out-of-memory termination are not interchangeable, and each needs its own evidence.

Third, **scaling is not a universal answer**. More replicas can help with demand, but they also consume capacity and may not help when the bottleneck is elsewhere.

Finally, **good incident resolution is collaborative and evidence-led**. It means sharing observations, challenging assumptions, choosing a justified action, and checking what changed afterward. It also means being clear about what the investigation proves—and what remains uncertain.

## Conclusion

Investigating resource-related problems in Amazon EKS is not just a matter of finding a high CPU graph or a restarted Pod. It means connecting application symptoms with resource behavior, distinguishing CPU throttling from memory-related failures, and selecting corrective actions based on evidence.

Working through API and ingestion workload issues with Prometheus, Grafana, and the team strengthened my approach to structured troubleshooting. The goal is not simply to get a workload running again; it is to understand the failure well enough to make a reasoned change and validate the result.

## Interview questions I use to test my understanding

- What is the difference between CPU throttling and `OOMKilled`?
- What evidence would you collect before changing CPU or memory requests and limits?
- How would you distinguish resource contention from a database or network bottleneck?
- What does the Horizontal Pod Autoscaler do, and what does it not do?
- Why might adding replicas fail to improve application latency?
- How would you verify that a remediation worked?

When discussing an incident in an interview, I aim to explain the evidence I reviewed, the reasoning behind the team's decisions, my own contribution, and what I learned—without claiming commands, configuration changes, or results I cannot substantiate.
