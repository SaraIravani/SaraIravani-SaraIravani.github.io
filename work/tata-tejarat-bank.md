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
  - Bamboo
  - Bitbucket
  - Ansible
  - AWX
  - JFrog
  - Prometheus
  - Grafana
  - Rancher
permalink: /work/tata-tejarat-bank/
---

## Overview

From September 2019 to January 2023, I worked as a Kubernetes / DevOps Engineer through TATA, supporting multiple application projects within the Tejarat Bank environment. I worked in a security-restricted environment where virtual machines had no direct Internet access, supporting application delivery and platform operations across different teams and workloads. Across these projects, more than 80 microservices ran on the platforms I supported.

## My Role and Contribution

I contributed to application delivery and platform operations across Dev, QA, staging, and production environments. My responsibilities covered container platforms, Kubernetes operations, CI/CD, artifact management, automation, security controls, and monitoring.

| Area | My Contribution |
|---|---|
| **Container Platforms** | Worked with Docker Compose, Docker Swarm, and Kubernetes across application projects. Updated deployment files and configurations for application settings, health checks, volumes, storage, and workload placement. |
| **Kubernetes Platform** | Contributed to provisioning and maintaining Kubernetes clusters using kubeadm and Ansible. Updated Ansible code as infrastructure and project requirements evolved and supported workload deployment and troubleshooting across environments. |
| **CI/CD** | Configured and modified Bamboo plans, templates, and pipeline steps connected to Bitbucket. Built container images and prepared deployment artifacts. Automated deployment steps in some projects, following project-specific release and approval procedures. |
| **Artifact Management** | Managed project dependencies and JFrog repository configuration, including creating virtual repositories and providing appropriate repository access to development teams. |
| **Security and Access Control** | Worked with Kubernetes namespaces, RBAC, and NetworkPolicies. Used SonarQube for code-quality checks and Trivy for container image vulnerability scanning within delivery workflows. |
| **Automation** | Used Ansible and shell scripts for infrastructure and operational tasks. Used AWX to execute recurring security patching, software installation, and other tasks across multiple virtual machines. |
| **Monitoring and Troubleshooting** | Used Prometheus and Grafana for monitoring, Rancher for workload visibility and log investigation, and Graylog for reviewing available logs. Investigated CI/CD failures, deployment issues, configuration problems, and Kubernetes workload health. |

## Environment and Constraints

The environment was based on VMware ESXi, with virtual machines that had no direct Internet access. Application dependencies and artifacts were managed through internal repositories and controlled access mechanisms.

JFrog provided an internal source for required dependencies and artifacts, with proxy-based access used where external connectivity was required.

I supported multiple application projects across separate Dev, QA, staging, and production environments. The deployment approach varied by project: some workloads used Docker Compose or Docker Swarm, while Kubernetes was used for other application workloads.

For the Kubernetes clusters, Keepalived and HAProxy provided a stable Kubernetes API endpoint. NGINX Ingress handled incoming application traffic through configured application domains.

## Architecture

The platform ran on VMware-based virtual machines and supported multiple application projects across different environments.

### Kubernetes API Endpoint

Keepalived provided a Virtual IP (VIP) for the Kubernetes API endpoint, while HAProxy handled traffic to the Kubernetes API servers.

```text
Kubernetes Clients
  kubectl / Ansible
        |
        v
  Keepalived VIP
        |
        v
     HAProxy
        |
        v
 Kubernetes API Servers
```

The stable endpoint allowed Kubernetes clients and administrative tools to access the API without connecting directly to individual control-plane nodes.

Control-plane node counts and etcd topology are omitted from this public case study.

### Application Traffic

NGINX Ingress handled incoming application traffic and routed requests to the appropriate Kubernetes services.

```text
Application Domain
        |
        v
  NGINX Ingress
        |
        v
 Kubernetes Service
        |
        v
  Application Pods
```

### CI/CD and Artifact Flow

Bitbucket was used for source control, and Bamboo supported build and delivery workflows. Internal repositories provided dependencies, container images, and deployment artifacts.

```text
Bitbucket
    |
    v
  Bamboo
    |
    +------> Container Image Build
    |                  |
    |                  v
    |         Internal Container Registry
    |
    +------> Deployment Manifest Preparation
                       |
                       v
               Internal Repository
```

The exact deployment steps varied by project. This diagram represents the build and artifact-preparation workflow and does not imply that every Bamboo pipeline directly applied Kubernetes manifests.

### Operations and Automation

Ansible supported infrastructure and operational automation, while AWX provided a way to execute tasks and workflows across multiple virtual machines. Prometheus and Grafana supported monitoring and operational visibility.

## Kubernetes Platform

I worked as part of the team responsible for provisioning and maintaining Kubernetes clusters across application environments, rather than owning the cluster architecture independently.

My work included:

- Updating and maintaining Ansible automation used for Kubernetes infrastructure.
- Supporting cluster and node-level operational tasks.
- Deploying and troubleshooting workloads across different environments.
- Working with Calico as the Kubernetes Container Network Interface (CNI).
- Working with NFS and Longhorn for application storage, including storage configuration and troubleshooting according to project requirements.

The clusters were provisioned using kubeadm, with Ansible used to automate and maintain infrastructure-related tasks.

## CI/CD and Artifact Management

I worked with Bamboo and Bitbucket Server to support application builds, container image creation, and deployment artifact preparation.

My contributions included:

- **Source control and builds:** Configured and maintained Bamboo plans connected to Bitbucket Server for source checkout and Maven builds.
- **Container image creation:** Built Docker images, tagged them with Bamboo build numbers, and pushed them to an internal container registry.
- **Deployment artifact preparation:** Updated Kubernetes YAML files with the corresponding image versions and published generated deployment files to an internal repository.
- **Pipeline configuration:** Implemented and modified Bamboo templates and pipeline configurations to meet project-specific requirements.
- **Quality and security checks:** Used SonarQube for code-quality checks and Trivy for container image vulnerability scanning.
- **Release troubleshooting:** Investigated pipeline failures and deployment issues and followed project-specific release approval procedures. Production releases could require approval from development teams or project management, depending on the project and environment.

The level of deployment automation varied by project. This work supported application delivery while accounting for internal repository requirements and restricted network access.

## Troubleshooting and Operational Challenges

I supported troubleshooting across Kubernetes workloads, containerized applications, and CI/CD workflows in a security-restricted banking environment. My approach focused on identifying the affected component, examining its current state and configuration, and determining an appropriate corrective action.

### Kubernetes Workload and Resource Troubleshooting

When investigating unhealthy workloads or resource-related issues, I examined Kubernetes node and pod status, resource conditions, pod descriptions, events, and relevant container logs.

The investigation involved distinguishing between node-level issues, workload configuration problems, and application-level failures. Depending on the findings, corrective actions could include correcting configuration, recovering affected workloads, or investigating node health and resource pressure.

### CI/CD and Deployment Troubleshooting

I investigated slow builds and deployment delays across Bamboo and Bitbucket workflows. This included reviewing pipeline execution, checking configuration and dependency definitions, and correcting issues in Docker Compose files or Kubernetes manifests where applicable.

After making a correction, I validated the relevant configuration and checked the resulting build or deployment behavior.

### Troubleshooting Under Restricted Network Access

The restricted environment introduced additional considerations when investigating build and deployment failures. Virtual machines had no direct Internet access, so dependencies, container images, and other required resources had to be obtained through internal repositories and available controlled access mechanisms.

Repository configuration, dependency availability, access permissions, and environment-specific settings were therefore important areas to check when troubleshooting delivery issues.

Specific production incidents, project configurations, and sensitive banking infrastructure details are omitted to respect confidentiality.

## Automation and Container Orchestration

I used Ansible, AWX, shell scripting, Docker Compose, and Kubernetes to support infrastructure operations and application deployment.

My responsibilities included:

- **Infrastructure automation:** Prepared and updated Ansible code and shell scripts for recurring security patching, software installation, and other operational tasks across multiple virtual machines.
- **AWX workflows:** Used AWX to execute Ansible tasks across multiple VMs when coordinated execution was required.
- **Docker Compose configuration:** Created and modified Compose files for application services, container settings, networking, volumes, and installation requirements.
- **Kubernetes configuration:** Developed and updated Kubernetes manifests based on application and project requirements.
- **Configuration maintenance:** Updated automation code, scripts, and deployment configurations as infrastructure and project requirements evolved.

These activities supported repeatable operational tasks and application configuration across different environments.

## Security and Access Control

I supported security controls across Kubernetes workloads, container images, CI/CD workflows, and internal artifact repositories.

My responsibilities included:

- **Kubernetes access control:** Defined namespaces according to environment and team requirements and used Kubernetes RBAC to manage access to namespace-scoped resources.
- **Network security:** Worked with Kubernetes NetworkPolicies as part of workload network isolation and access control.
- **Container vulnerability scanning:** Used Trivy to scan container images for known vulnerabilities within application delivery workflows.
- **Code quality:** Used SonarQube for code-quality checks in CI/CD workflows.
- **Artifact and dependency management:** Configured JFrog virtual repositories, added required dependencies, and managed repository access for development teams.
- **Infrastructure security maintenance:** Used Ansible and AWX to support recurring security patching across multiple virtual machines.
- **Controlled releases:** Followed project-specific production release approval procedures.

The environment's network restrictions and reliance on internal repositories made controlled dependency and artifact management an important part of application delivery.

## Monitoring and Observability

I supported day-to-day monitoring and troubleshooting of Kubernetes workloads and applications in collaboration with the SRE team.

My responsibilities included:

- Using Prometheus and Grafana to monitor Kubernetes and application health and review relevant metrics.
- Using Rancher to inspect cluster and workload status and investigate application or container issues.
- Reviewing available logs in Rancher and Graylog to help identify errors and investigate failures.
- Combining monitoring information, workload status, and logs to narrow down potential causes before taking corrective action.

These tools supported operational visibility and troubleshooting across the environments I worked with.

## Constraints and Trade-offs

Several environmental constraints influenced how applications were built, deployed, and maintained:

- **Restricted network access:** Virtual machines relied on internal repositories and controlled access mechanisms rather than direct Internet access.
- **Existing infrastructure:** Kubernetes workloads operated on VMware-based virtual machines, with platform configuration shaped by the available infrastructure.
- **Controlled software delivery:** Build and delivery workflows relied on internal artifact repositories and established CI/CD processes.
- **Multiple deployment models:** Supporting Docker Compose, Docker Swarm, and Kubernetes required adapting configuration and operational practices to the application and environment.
- **Release controls:** Production delivery followed project-specific approval requirements, which varied across teams and environments.

These constraints formed part of the operational context for supporting application platforms in a restricted enterprise banking environment.

## Technologies

- **Container platforms:** Kubernetes, Docker, Docker Compose, Docker Swarm
- **Kubernetes provisioning and networking:** kubeadm, Ansible, Calico, NGINX Ingress
- **Storage:** NFS, Longhorn
- **Virtualization and API traffic handling:** VMware ESXi, HAProxy, Keepalived
- **CI/CD and source control:** Bamboo, Bitbucket Server
- **Build and dependency management:** Maven, JFrog
- **Automation and scripting:** Ansible, AWX, Shell Scripting
- **Monitoring and observability:** Prometheus, Grafana, Alertmanager, Rancher, Graylog
- **Security and code quality:** Kubernetes RBAC, NetworkPolicies, Trivy, SonarQube
- **Container administration:** Portainer

## What I Learned

Working in a restricted banking environment strengthened my understanding of operating Kubernetes platforms under security, access-control, and infrastructure constraints.

I gained practical experience supporting application delivery across multiple environments, investigating Kubernetes and CI/CD issues, managing team access through namespaces and RBAC, and automating recurring operational tasks with Ansible and AWX.

The experience reinforced the importance of internal artifact management, environment-specific configuration, monitoring, and collaboration when supporting application platforms across multiple projects.
