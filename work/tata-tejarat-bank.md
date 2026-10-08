---
title: "TATA / Tejarat Bank"
date: 2023-01-01
type: project
lang: en
layout: case-study
role: "Kubernetes / DevOps Engineer"
technologies:
  - Kubernetes
  - Docker
  - Docker Swarm
  - Docker Compose
  - CI/CD
  - Automation
  - Monitoring
permalink: /work/tata-tejarat-bank/

---

## Overview

From September 2019 to January 2023, I worked as a Kubernetes / DevOps Engineer in Tejarat Bank's environment, through TATA. I worked across multiple enterprise banking projects in a highly restricted, air-gapped environment, supporting application delivery and platform operations across different teams and workloads. Across these projects, more than 80 microservices were running on the platforms I supported. The environment evolved over time, with some workloads initially using Docker Compose and Docker Swarm before Kubernetes became the main orchestration platform. As part of the team, I worked hands-on in Kubernetes operations, application deployment, CI/CD, automation, monitoring, security-related activities, and production troubleshooting.

## My Role and Contribution

As a Kubernetes / DevOps Engineer, I worked across multiple application projects within the Tejarat Bank environment, supporting the delivery and operation of more than 80 microservices across Dev, QA, staging, and production environments.

My hands-on contribution included:

| Area | My Contribution |
|---|---|
| **Deployment & Container Platforms** | Worked hands-on with application deployments across Docker Compose and Kubernetes, with earlier workloads also running on Docker Swarm. Modified deployment files and configurations based on project requirements, including application configuration, login configuration, health checks, volumes and shared volumes, storage selection, and workload placement across different worker nodes and environments. |
| **Kubernetes Platform** | Worked as part of a team to provision and maintain Kubernetes clusters using Ansible. Updated Ansible code as infrastructure and project requirements evolved. Supported workload deployment across different environments and worker nodes. |
| **CI/CD** | Supported CI/CD pipelines in Bamboo and Bitbucket across Dev, QA, staging, and production. Implemented and modified Bamboo templates and deployment configurations according to project requirements. Automated deployment steps in some projects. Production deployments could require approval from the development team or project management, depending on the project and environment. |
| **Artifact Management** | Had responsibility for project dependency management in JFrog. Added required project dependencies, created and configured virtual repositories, and provided appropriate repository access for software development teams using JFrog. |
| **Security & Access Control** | Used Rancher to monitor workloads and inspect logs. Defined namespaces based on environment and team access requirements, and used Kubernetes RBAC to manage access to namespaces and resources for different teams. Also worked with NetworkPolicies. Used SonarQube for code-quality checks within the CI/CD process and Trivy for container image scanning. |
| **Automation** | Used Ansible for infrastructure and operational automation. Used AWX workflows for tasks that needed to run across multiple VMs simultaneously, including recurring security patching and other multi-VM installation or operational tasks. |
| **Monitoring & Troubleshooting** | Worked with Prometheus and Grafana and used Rancher for operational monitoring and log investigation. Troubleshot issues across CI/CD and Kubernetes environments, including pipeline and deployment failures, workload issues, configuration problems, and monitoring-related issues. |

## Environment and Constraints

I worked in a highly restricted environment within the Tejarat Bank infrastructure. The infrastructure was based on VMware ESXi, and the virtual machines had no direct Internet access.

External dependencies and Internet-dependent requirements were handled through controlled internal access. Application teams used internal JFrog repositories for required dependencies and artifacts, with proxy-based access used where external connectivity was required.

The environment included multiple application projects and separate Dev, QA, staging, and production environments, with more than 80 microservices running across the projects I supported.

For the Kubernetes clusters, HAProxy and Keepalived were used for cluster traffic and high availability, with NGINX Ingress used as the ingress layer for application traffic.

The platform evolved from Docker Compose and Docker Swarm workloads to Kubernetes as the main orchestration platform, so I supported different deployment models across projects.

The platform evolved over time. Some workloads initially ran using Docker Compose and Docker Swarm, while Kubernetes later became the main container orchestration platform. This required supporting different deployment models and operational requirements during the transition and across different application projects.

## Architecture

The environment was built on VMware ESXi, with virtual machines supporting the Kubernetes platform and application workloads. The architecture supported multiple application projects across Dev, QA, staging, and production environments.

### Kubernetes API Endpoint

The Kubernetes clusters used Keepalived and HAProxy to provide a stable Kubernetes API endpoint. Keepalived provided a Virtual IP (VIP) for the Kubernetes control-plane endpoint, while HAProxy handled traffic to the Kubernetes API servers.

The Kubernetes API endpoint was used by Kubernetes clients and administrative tools such as `kubectl` and Ansible, without requiring clients to connect directly to individual control-plane nodes.

```text
Kubernetes Clients
kubectl / Ansible
        │
        ▼
   Keepalived VIP
 Kubernetes API Endpoint
        │
        ▼
     HAProxy
        │
        ▼
Kubernetes Control Plane
   API Servers
```

Control-plane node counts and etcd topology are omitted from this public write-up.

### Application Traffic

Application traffic was handled separately from the Kubernetes API path. NGINX Ingress was used to expose applications through their configured domains and route incoming requests to the appropriate Kubernetes services.

```text
User
  │
  ▼
Application Domain
  │
  ▼
NGINX Ingress
  │
  ▼
Kubernetes Service
  │
  ▼
Application Pods
```

### CI/CD and Delivery

Bitbucket was used for source control and Bamboo for CI/CD. Bamboo pipelines were used to build and deploy applications across the different environments.

```text
Bitbucket
    │
    ▼
  Bamboo
    │
    ├───────────────┐
    ▼               ▼
Kubernetes       Docker Compose
Environments     Workloads
```

Internal JFrog repositories were used to provide application dependencies and required artifacts within the restricted environment.

### Operations and Automation

Ansible was used for infrastructure and operational automation, while AWX was used to execute operational tasks and workflows across multiple virtual machines when required.

Prometheus and Grafana were used for monitoring and operational visibility.

### Architecture Summary

* **Kubernetes API Endpoint:** Keepalived → Virtual IP → HAProxy → Kubernetes API servers
* **Application Runtime:** Application Domain → NGINX Ingress → Kubernetes Service → Application Pods
* **CI/CD:** Bitbucket → Bamboo → Deployment environments
* **Operations:** Ansible / AWX for automation; Prometheus / Grafana for monitoring


## Kubernetes Platform

I worked as part of the team responsible for provisioning and maintaining Kubernetes clusters across multiple application environments, rather than owning the cluster architecture independently.

My hands-on work included updating and maintaining Ansible automation used for Kubernetes infrastructure, supporting cluster and node-level operational tasks, and deploying and troubleshooting workloads across different environments.

The Kubernetes clusters were provisioned using kubeadm, with Ansible used to automate and maintain infrastructure-related tasks.

Calico was used as the Kubernetes CNI. I worked with Kubernetes networking as part of day-to-day platform operations.

For storage, I worked with NFS and Longhorn, including storage configuration and troubleshooting according to application and project requirements.


## CI/CD

[To be completed]

## Automation

[To be completed]

## Security

[To be completed]

## Monitoring and Operations

[To be completed]

## Operations and Incidents

[To be completed]

## Decisions and Trade-offs

[To be completed]

## Technologies

[To be completed]

## What I Learned

[To be completed]
