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

From September 2019 to January 2023, I worked as a Kubernetes / DevOps Engineer in Tejarat Bank's environment, through TATA. I worked across multiple enterprise banking projects in a highly restricted, air-gapped environment, supporting application delivery and platform operations across different teams and workloads. Across these projects, more than 80 microservices were running on the platforms I supported.

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

The platform evolved over time. Some workloads initially ran using Docker Compose and Docker Swarm, while Kubernetes later became the main container orchestration platform. This required supporting different deployment models and operational requirements during the transition and across different application projects.

## Architecture

The environment was built on VMware ESXi, with virtual machines supporting the Kubernetes platform and application workloads. The architecture supported multiple application projects across Dev, QA, staging, and production environments.

Kubernetes API Endpoint: Keepalived → Virtual IP → HAProxy → Kubernetes API servers

Application Runtime: Application Domain → NGINX Ingress → Kubernetes Service → Application Pods

CI/CD: Bitbucket → Bamboo → Deployment environments

Operations: Ansible / AWX for automation; Prometheus / Grafana for monitoring


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


## CI/CD and Artifact Management

I worked with Bamboo and Bitbucket Server to support application build, container image creation, and deployment artifact preparation in the restricted banking environment.

My contributions included:

* **Source control and build:** Configured and maintained Bamboo plans connected to Bitbucket Server for source checkout and Maven builds.
* **Docker image build and publishing:** Built Docker images, tagged them with Bamboo build numbers, and pushed them to an internal container registry.
* **Deployment artifact preparation:** Updated Kubernetes YAML files with the corresponding image version and published the generated deployment files to an internal repository.
* **Pipeline configuration:** Implemented and modified Bamboo templates and deployment configurations to meet project-specific requirements.
* **Code quality and image security:** Used SonarQube for code-quality checks and Trivy for container image vulnerability scanning.
* **Troubleshooting and release procedures:** Investigated CI/CD pipeline failures and deployment issues and followed project-specific release approval procedures. Production deployments could require approval from the development team or project management, depending on the project and environment.

The pipeline steps and level of deployment automation varied by project. Internal repositories were important for managing application dependencies, container images, and deployment artifacts in an environment without direct Internet access.

## Troubleshooting and Operational Resilience

Supported Kubernetes platforms in a security-restricted banking environment, troubleshooting infrastructure, workload, storage, and configuration issues across controlled environments.

My troubleshooting approach focused on identifying whether an issue originated at the node, cluster, or application level. I used Kubernetes status information, resource descriptions, events, container and system logs, and monitoring data to investigate failures and determine appropriate corrective actions.

Depending on the issue, remediation included configuration corrections, workload recovery, storage and connectivity validation, and node-level recovery when required. I also investigated resource pressure, scheduling constraints, persistent volume provisioning, container image availability, and application configuration.

### CI/CD and Deployment Troubleshooting

Investigated slow builds and deployment delays across Bamboo and Bitbucket CI/CD workflows. Troubleshooting included reviewing pipeline execution and configuration, identifying dependency-related issues, and correcting dependency definitions in Docker Compose files or Kubernetes manifests where applicable. Validated the resulting configuration and deployment behavior to help ensure reliable application delivery.

Specific production incidents, project configurations, and environment details are omitted to respect banking confidentiality.


## Automation and Container Orchestration

I used Ansible, AWX, shell scripting, Docker Compose, and Kubernetes to support infrastructure operations and application deployment in the restricted banking environment.

My responsibilities included:

* **Infrastructure automation:** Prepared and updated Ansible code and shell scripts for recurring security patching and software installation across multiple virtual machines, using AWX to execute Ansible tasks.
* **Docker Compose configuration:** Created and modified Docker Compose files to configure application services, container settings, networking, and installation requirements.
* **Kubernetes configuration:** Developed and updated Kubernetes manifests based on application and project requirements.
* **Container orchestration:** Worked with Docker Compose and Kubernetes to configure and manage application deployments across different environments.
* **Configuration maintenance:** Updated automation code, scripts, and deployment configurations as infrastructure and project requirements evolved.

This work helped reduce repetitive manual tasks and supported consistent infrastructure operations and application deployments in a restricted enterprise environment.


## Security and Access Control

I supported security controls across Kubernetes workloads, container images, CI/CD workflows, and internal artifact repositories in a restricted enterprise banking environment.

My responsibilities included:

* **Kubernetes access control:** Defined namespaces according to environment and team requirements and used Kubernetes RBAC to manage access to namespace-scoped resources.
* **Network security:** Worked with Kubernetes NetworkPolicies as part of workload network isolation and access control.
* **Container vulnerability scanning:** Used Trivy to scan container images for known vulnerabilities as part of application delivery workflows.
* **Code quality checks:** Used SonarQube to assess code quality within CI/CD workflows.
* **Artifact and dependency management:** Created and configured JFrog virtual repositories, added required project dependencies, and managed repository access for software development teams.
* **Infrastructure security maintenance:** Prepared and updated Ansible code for recurring security patching across multiple virtual machines, using AWX to execute the automation.
* **Controlled release processes:** Followed project-specific production release approval procedures, which varied by environment and project.

Because the environment had no direct Internet access from servers and virtual machines, internal repositories were essential for obtaining application dependencies and managing container images and other delivery artifacts.

These responsibilities gave me practical experience applying access controls, supporting vulnerability management, automating security maintenance, and operating application delivery workflows within a security-restricted banking environment.


## Monitoring and Observability

I supported application and Kubernetes cluster monitoring in collaboration with the SRE team, using Prometheus, Grafana, Alertmanager, Logstash, Graylog, Rancher, and Portainer.

My responsibilities included:

* **Prometheus monitoring:** Installed and configured exporters as required to collect metrics from applications and infrastructure components.
* **Grafana dashboards and queries:** Wrote and updated Grafana queries to support application and infrastructure monitoring in collaboration with the SRE team.
* **Alerting:** Worked with Alertmanager as part of the monitoring and alerting setup.
* **Log analysis:** Used Graylog and Rancher to inspect application and workload logs and investigate operational issues.
* **Kubernetes monitoring:** Used Prometheus and Grafana to monitor Kubernetes clusters and application workloads.
* **Container management:** Used Rancher and Portainer to inspect and manage containerized workloads in the environments where they were used.
* **Troubleshooting:** Collaborated with the SRE and other technical teams to investigate application, workload, and infrastructure issues.

This experience strengthened my practical understanding of metrics collection, dashboard queries, alerting, centralized log analysis, and Kubernetes operations in an enterprise environment.

## Challenges

**1. Resource Management and Workload Stability**  
Managed and troubleshot workloads in Docker Compose and Kubernetes environments, where resource usage could affect the stability of other services running on shared infrastructure.

**2. Troubleshooting in a Restricted Environment**  
Worked in an air-gapped banking environment where external internet access was restricted. Application dependencies and container images had to be managed through internal repositories and controlled access.

**3. Deployment Reliability Across Environments**  
Supported application deployments across Dev, QA, staging, and production, troubleshooting configuration differences, deployment failures, and workload health issues.


## Operations and Incidents

Operational troubleshooting involved investigating issues across Kubernetes workloads, application deployments, and CI/CD pipelines.

My approach was to examine the available logs and monitoring information, review the relevant deployment configuration, and investigate the affected workload or pipeline to identify the source of the problem.

In this restricted environment, troubleshooting also required considering environment-specific configuration, internal artifact availability, access permissions, and differences between deployment environments.

I worked within the team to investigate and resolve issues, supporting the stability of application delivery and day-to-day platform operations.

## Decisions and Trade-offs

The environment required balancing application delivery, operational consistency, and security restrictions.

Several important considerations shaped the way the platform was operated:

* **Internal artifact management:** Internal JFrog repositories were necessary because servers and virtual machines did not have direct Internet access.
* **Environment-specific deployments:** Deployment configurations had to accommodate differences between Dev, QA, staging, and production.
* **Controlled production releases:** Approval requirements depended on the project and environment, supporting controlled changes to production systems.
* **Team-based access control:** Namespaces and RBAC were used to organize workloads and manage access for different teams.
* **Operational automation:** Ansible and AWX helped execute recurring and multi-VM tasks more consistently.
* **Evolution of container platforms:** The environment included earlier Docker Compose and Docker Swarm workloads, with Kubernetes becoming the primary orchestration platform over time.

These considerations reflect the operational constraints of supporting application platforms in a restricted enterprise banking environment.

## Technologies

* **Container platforms:** Kubernetes, Docker, Docker Compose, Docker Swarm
* **Kubernetes management:** Rancher, kubeadm
* **Virtualization and traffic handling:** VMware ESXi, HAProxy
* **CI/CD and source control:** Bamboo, Bitbucket
* **Artifact and dependency management:** JFrog
* **Automation:** Ansible, AWX
* **Monitoring and observability:** Prometheus, Grafana
* **Security and code quality:** Kubernetes RBAC, NetworkPolicies, Trivy, SonarQube
* **Networking:** Calico

## What I Learned

Working in a restricted banking environment strengthened my understanding of operating Kubernetes platforms under security, access-control, and infrastructure constraints.

I gained practical experience supporting application delivery across multiple environments, investigating Kubernetes and CI/CD issues, managing team access through namespaces and RBAC, and automating recurring operational tasks with Ansible and AWX.

The experience also reinforced the importance of internal artifact management, environment-specific deployment configuration, monitoring, and collaboration when supporting production workloads across multiple application projects.

