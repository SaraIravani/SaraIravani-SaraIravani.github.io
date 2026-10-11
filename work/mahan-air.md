---
title: "Infrastructure, Observability & DevOps Team Development — Mahan Air"
date: 2019-09-01
type: project
lang: en
layout: case-study
role: "Systems Administration, Observability & DevOps"
technologies:
  - VMware vSphere
  - VMware ESXi
  - Team Foundation Server (TFS)
  - Apache Tomcat
  - Elasticsearch
  - Logstash
  - Kibana
  - Kibana APM
  - SCOM
  - Windows Server
  - Active Directory
  - LDAP
  - PowerShell
  - Shell Scripting
permalink: /work/mahan-air/
---

# Infrastructure, Observability & DevOps Team Development

**Organization:** Mahan Air  
**Period:** April 2013 – September 2019  
**Focus:** Infrastructure operations, application observability, performance troubleshooting, delivery automation, and team development

## Overview

At Mahan Air, I progressed from systems administration and coordinating between software teams and the data center to forming and organizing a five-person DevOps team.

I supported enterprise environments spanning Microsoft-based and Java-based applications. My responsibilities included VMware infrastructure, centralized logging and application performance monitoring, TFS-based CI/CD workflows, and scripting for operational tasks.

A practical example was supporting the investigation of increased request volume in an ERP application. I used Elasticsearch and Kibana to monitor application behavior while the team adjusted Tomcat's request-processing thread limit and JVM heap settings.

## Key Contributions

### Infrastructure Operations

I managed VMware vSphere and ESXi infrastructure supporting the software team's environment, including six hosts. I also served as a technical contact point between software developers and the data center, coordinating application requirements with infrastructure operations.

The environment included Windows Server, Active Directory, and LDAP. This role developed my understanding of the infrastructure dependencies behind enterprise application delivery and the coordination needed between development and operations teams.

### Application Observability

I worked with a colleague to monitor Java and JavaScript applications, including ERP-related applications, using Elasticsearch, Logstash, Kibana, and Kibana APM.

My work included exploring application logs, preparing queries, and reviewing performance-related information to support investigations.

For Microsoft-based projects, I collaborated with a colleague on a SCOM implementation over approximately six months. After implementation, we handed the solution over to the data center team.

These projects gave me experience with application-level monitoring as well as monitoring for Microsoft-based environments.

### ERP Performance Investigation: Increased Request Volume

**Problem**

Request volume increased significantly for an ERP application running on Apache Tomcat. The team needed to accommodate greater concurrency while considering application and server resource limits.

**Investigation**

I used Elasticsearch and Kibana to review application logs and performance-related information. Our QA team used Apache JMeter to simulate concurrent requests and test application behavior under load.

**Team response**

The team increased Tomcat's `maxThreads` setting and adjusted JVM heap settings to accommodate the workload.

**Engineering considerations**

Increasing `maxThreads` allows Tomcat to process more requests concurrently, but it does not automatically improve throughput. If the application is limited by CPU, memory, database connections, or another shared resource, additional threads can increase contention and resource pressure. JVM heap sizing also needs to be considered alongside thread-stack memory and the available system memory.

**My contribution and learning**

My contribution was monitoring application logs and performance-related information through Elasticsearch and Kibana as part of the team's investigation. The Tomcat and JVM configuration changes were a team effort.

This experience reinforced the importance of investigating application behavior and resource constraints rather than treating a higher thread limit as a guaranteed performance fix. No quantified improvement is claimed because a measured result has not been established.

### CI/CD and Operational Automation

I worked with Team Foundation Server (TFS) to support CI/CD and application deployment workflows. I also used PowerShell and shell scripting for administrative automation and repetitive operational tasks.

This work connected systems administration with software delivery, where repeatable workflows and reduced manual effort help make operational processes more consistent.

### Forming and Organizing a DevOps Team

I formed and organized a five-person DevOps team focused on:

- Application monitoring and observability.
- CI/CD and deployment workflows.
- Scripting and operational automation.
- Infrastructure and systems operations.
- Supporting development teams with deployments.

This expanded my responsibilities from individual infrastructure work to organizing a team across several connected areas of delivery and operations.

## Transferable Engineering Capabilities

- **Observability and investigation:** Using centralized logs and application performance information to investigate application behavior.
- **Performance and resource awareness:** Understanding concurrency, thread capacity, and memory considerations when supporting Java applications under increased load.
- **Operational ownership and coordination:** Connecting infrastructure operations, deployment workflows, and development teams, while organizing a team around these responsibilities.

These are transferable foundations for DevOps and platform engineering. The environment described here predates my later cloud-native work and is not presented as Kubernetes or AWS experience.

## Technologies

**Virtualization:** VMware vSphere, VMware ESXi

**Monitoring and observability:** Elasticsearch, Logstash, Kibana, Kibana APM, SCOM

**Application environment:** Apache Tomcat, Java, JavaScript, Microsoft-based applications, ERP-related applications

**CI/CD:** Team Foundation Server (TFS)

**Systems administration:** Windows Server, Active Directory, LDAP

**Scripting:** PowerShell, shell scripting

*This case study describes my responsibilities and collaborative work. Internal hostnames, subdomains, network details, and confidential architecture have been omitted.*
