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

I worked in a highly restricted, air-gapped environment within the Tejarat Bank infrastructure. The servers and virtual machines did not have direct Internet access, so internal repositories and controlled infrastructure were required for application delivery and platform operations.

The environment included multiple application projects and separate Dev, QA, staging, and production environments. Across the projects I supported, more than 80 microservices were running on the platforms.

The infrastructure was based on VMware ESXi, with HAProxy used within the environment for application traffic handling. Internal JFrog repositories were used to provide required application dependencies and artifacts without relying on direct Internet access.

The platform evolved over time. Some workloads initially ran using Docker Compose and Docker Swarm, while Kubernetes later became the main container orchestration platform. This required supporting different deployment models and operational requirements during the transition and across different application projects.

## Environment and Constraints

[To be completed]

## Architecture

[To be completed]

## Kubernetes Platform

[To be completed]

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
