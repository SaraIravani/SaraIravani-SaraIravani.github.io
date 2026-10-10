---
title: "AWS Flight Pricing Platform — Yekta Pardazan Novin Caspian"
date: 2026-01-01
type: project
lang: en
layout: case-study
role: "Platform / DevOps Engineer"
technologies:
  - AWS
  - Amazon EKS
  - Kubernetes
  - Terraform
  - Docker
  - Jenkins
  - GitHub
  - Amazon ECR
  - Amazon RDS
  - Amazon S3
  - IAM
  - Prometheus
  - Grafana
permalink: /work/yekta-pardazan/
---

# AWS Flight Pricing Platform

**Role:** Platform / DevOps Engineer  
**Period:** March 2023 – January 2026  
**Platform:** AWS, Amazon EKS, Terraform, Kubernetes

## Overview

I worked on the infrastructure and delivery workflows for a high-availability B2B flight-pricing platform that aggregated airline and travel-agency data from multiple sources.

The platform ran on AWS, using Amazon EKS to orchestrate containerized workloads, Amazon RDS for PostgreSQL, and Amazon S3 for object storage. Its workload mix included API services, data ingestion, data processing, batch jobs, and supporting platform components.

My work focused on AWS infrastructure provisioning, Kubernetes workload placement, Terraform automation, container delivery, IAM and workload security, monitoring, and production troubleshooting.

The key engineering challenges involved keeping infrastructure changes isolated across environments, matching compute capacity to workload requirements, maintaining private image access, and controlling AWS permissions at workload level.

## My Role and Contributions

My responsibilities covered the implementation and operation of platform infrastructure and delivery workflows.

- Provisioned and managed AWS infrastructure using Terraform.
- Worked with Amazon EKS, VPC networking, IAM, EC2 capacity, Amazon ECR, Amazon RDS, and Amazon S3.
- Managed Kubernetes node groups and workload placement to address different resource and availability requirements.
- Supported Docker image build and delivery workflows using GitHub and Jenkins.
- Applied infrastructure deployment safeguards across development, staging, and production.
- Implemented workload-level AWS access using IAM Roles for Service Accounts (IRSA).
- Investigated Kubernetes scheduling, resource-pressure, registry-connectivity, and IAM permission issues.
- Used Prometheus and Grafana to support infrastructure and workload monitoring.
- Worked with Trivy, SonarQube, and JFrog as part of the software delivery toolchain.

My contribution focused on platform and DevOps engineering. It did not mean ownership of every application or the entire platform architecture.

## Architecture

The platform combined AWS infrastructure, Kubernetes workloads, and managed data services.

```text
Airline and Travel-Agency Data Sources
                  |
                  v
       Ingestion Workloads
          on Amazon EKS
                  |
                  v
       Data Processing Workloads
          on Amazon EKS
                  |
          +-------+-------+
          |               |
          v               v
      Amazon S3       Amazon RDS
    Object Storage    PostgreSQL
                          ^
                          |
                    API Workloads
                    on Amazon EKS


Delivery Workflow:
GitHub → Jenkins → Docker Build → Amazon ECR

Infrastructure:
Terraform → AWS Resources
```

Amazon EKS provided the orchestration layer for containerized application workloads. Amazon RDS and S3 supported the platform's data requirements, while Amazon ECR stored container images used by Kubernetes workloads.

The infrastructure and delivery layers had different responsibilities: Terraform managed infrastructure, while the container delivery workflow handled source changes, builds, and image publication.

## Kubernetes Workload Isolation

### Dedicated node groups

The platform used separate node groups for different workload categories rather than treating all worker nodes as one shared pool.

| Workload category | Capacity approach | Engineering rationale |
|---|---|---|
| System | On-Demand | Maintain predictable capacity for core platform components |
| API | On-Demand | Support application workloads requiring predictable performance |
| Ingestion | Mixed capacity | Balance compute requirements, availability, and cost |
| Data processing | Dedicated capacity | Accommodate workloads with distinct resource requirements |
| Batch | Spot | Reduce compute costs for interruption-tolerant jobs |
| Security | Dedicated On-Demand capacity | Separate security-related components from general application workloads |

This public summary intentionally omits exact instance types, configured node-count ranges, and purchasing percentages.

### Scheduling and capacity decisions

Kubernetes taints, tolerations, and affinity rules provided controls for directing workloads to their intended node groups.

The design balanced three competing requirements:

1. **Availability:** Keep critical application workloads on predictable On-Demand capacity.
2. **Cost:** Use Spot capacity where workload interruption tolerance made it appropriate.
3. **Isolation:** Reduce competition between workloads with different CPU and memory requirements.

The choice of Spot was workload-dependent rather than a blanket cost-saving measure. Batch processing could use more aggressive scaling, while API workloads required a more predictable capacity strategy.

## Infrastructure as Code with Terraform

Terraform was used to provision and manage AWS infrastructure, including VPC resources, IAM configuration, EC2 capacity, and EKS-related infrastructure.

The infrastructure workflow used:

- Version-controlled Terraform configuration.
- Amazon S3 for remote state storage.
- DynamoDB for state locking.
- Environment-specific configuration for development, staging, and production.

Remote state allowed infrastructure state to be managed outside individual workstations, while state locking helped coordinate concurrent Terraform operations.

A production incident involving incorrect backend configuration exposed the need for stronger separation between environment selection and infrastructure execution. The resulting safeguards are described in the first troubleshooting case below.

## CI/CD and Container Delivery

The delivery workflow used GitHub, Jenkins, Docker, and Amazon ECR.

The main stages were:

1. Source code was maintained in GitHub.
2. Jenkins supported the build and delivery workflow.
3. Docker images were built for application workloads.
4. Images were published to Amazon ECR.
5. Kubernetes workloads consumed the published images.

Trivy and SonarQube supported security and code-quality checks within the delivery toolchain. JFrog was used for artifact management.

Separating image creation and registry publication from infrastructure provisioning made the responsibilities of the build and infrastructure workflows clearer.

## IAM, Workload Identity, and Security

### IAM Roles for Service Accounts

Some Kubernetes workloads needed access to AWS services, including S3 and AWS-related storage operations.

I implemented IAM Roles for Service Accounts (IRSA) to provide workload-specific AWS permissions through Kubernetes service accounts.

The implementation involved:

- Enabling the EKS cluster's OIDC identity integration.
- Creating IAM roles for the workloads that required AWS access.
- Associating the roles with the relevant Kubernetes service accounts.
- Aligning workload permissions with their specific AWS access requirements.

This reduced reliance on shared worker-node IAM permissions for workloads that needed different access levels.

The approach supported least-privilege access by making AWS permissions more specific to workload identity instead of assigning all permissions at node level.

### Private registry connectivity

The platform also required a secure path for private-subnet worker nodes to retrieve container images from Amazon ECR.

VPC endpoint configuration, DNS, security groups, and IAM permissions were important parts of this connectivity path. A failure in this layer caused a deployment incident described below.

## Monitoring and Observability

Prometheus and Grafana supported monitoring of infrastructure and Kubernetes workloads.

The operational focus included resource pressure, workload behavior, and application symptoms such as increased latency. Kubernetes resource metrics and scheduling information helped investigate whether issues were related to workload configuration or available node capacity.

Monitoring was also relevant when validating infrastructure and scheduling changes. Where a quantified improvement is reported, it should reflect an actual measurement rather than an assumed result.

## Selected Engineering Challenges

### 1. Terraform State Isolation and Production Safety

**Problem**

The platform used Terraform across development, staging, and production, with remote state stored in S3 and locking managed through DynamoDB.

A team member intended to apply a change to staging, but the backend configuration pointed to the production state. The changes intended for staging were consequently applied to production, modifying resources unexpectedly and introducing state drift and potential downtime risk.

**Investigation and root cause**

I investigated the Terraform execution and identified two contributing weaknesses:

- The backend configuration did not correctly isolate the intended environment.
- The workflow lacked sufficient validation before applying infrastructure changes.

The incident showed that separate environment configurations are not enough when the execution process does not verify which environment and state are being targeted.

**Remediation**

I introduced four safeguards:

1. **Separate state paths:** Enforced distinct S3 state-key paths for development, staging, and production.
2. **Plan review:** Made `terraform plan` a required review step before applying changes.
3. **Production approval:** Routed production infrastructure changes through controlled CI/CD pipelines with manual approval.
4. **Pre-apply validation:** Added checks to verify the intended environment and backend configuration before Terraform execution.

**Outcome**

These controls strengthened environment isolation and reduced the risk of unintended production changes. They also made infrastructure changes more reviewable and established a more controlled process for production deployments.

**Engineering lesson:** Terraform state isolation is part of production change management. Backend configuration, plan review, and deployment authorization need to work together.

### 2. EKS ImagePullBackOff After Moving Worker Nodes to Private Subnets

**Problem**

During a security-hardening initiative, worker nodes were moved into private subnets to reduce direct Internet exposure.

After the change, newly deployed pods entered the `ImagePullBackOff` state because the worker nodes could not retrieve their container images from Amazon ECR.

**Investigation**

I used `kubectl describe pod` to inspect the affected workloads and investigated worker-node connectivity and VPC networking behavior.

The investigation connected the deployment failure to the network path required for private-subnet nodes to access the container registry.

**Root cause**

The required Amazon ECR VPC endpoints had not been configured after the worker nodes were moved into private subnets.

Without the required private connectivity, the nodes could not retrieve images from ECR through the intended network path.

**Resolution**

I configured the required ECR interface VPC endpoints and validated the associated security-group and DNS configuration.

**Validation and impact**

After private connectivity was established, the nodes were able to pull the required images and the affected deployments recovered. The environment could retain private worker-node networking without relying on direct Internet access for image retrieval.

**Engineering lesson:** A Kubernetes deployment can fail because of an underlying cloud-networking dependency. Successful image delivery requires the registry, network path, DNS, and access controls to work together.

### 3. Workload-Level IAM Permissions with IRSA

**Problem**

Some workloads needed access to AWS services, including S3, while a storage CSI driver required permissions for AWS-related storage operations.

Some pods encountered `AccessDenied` errors because the existing IAM permissions were not appropriate for every workload.

**Investigation**

I reviewed pod logs and investigated the permissions available to the failing workloads.

Some AWS access depended on the worker-node IAM role. This approach was insufficient for workloads with different permission requirements: workloads that did not need AWS access were unaffected, while some applications and storage operations lacked the permissions they required.

**Resolution**

I implemented IRSA by enabling OIDC integration, creating dedicated IAM roles, and associating those roles with Kubernetes service accounts.

This allowed supported workloads to obtain AWS permissions through their own service-account identity instead of relying solely on the shared node role.

**Security impact**

The change improved workload-level permission isolation and supported least-privilege access. It also reduced the need to grant broad worker-node permissions to support individual applications.

**Engineering lesson:** Workload identity is a critical part of EKS security. Dedicated IAM roles associated with service accounts provide a more granular access model than relying on a shared node role.

## Capacity and Cost Optimization

The platform used different EC2 instance families and purchasing strategies to reflect workload requirements.

Spot capacity was used for selected ingestion and batch workloads, while On-Demand instances supported system and API workloads. Dedicated node groups provided a way to manage capacity and workload placement independently.

Infrastructure cost optimization involved matching capacity to workload behavior rather than minimizing instance cost in isolation. Reliability, interruption tolerance, scaling requirements, and operational recovery all influenced the appropriate capacity strategy.

## Operational Outcomes

The work focused on improving the repeatability of infrastructure provisioning, controlling production changes, and aligning Kubernetes capacity with application requirements.

The reported outcomes included:

- Reducing infrastructure provisioning effort from days to hours.
- Moving from weekly releases to multiple releases per day.
- Achieving approximately 30% lower infrastructure cost through capacity and node-group optimization.
- Supporting a platform handling several hundred thousand transactions per day.

These figures describe the reported operational results associated with the platform. Their measurement basis and attribution should remain consistent with the actual project records and the specific changes I contributed to.

## Constraints and Engineering Considerations

The work involved several connected operational concerns:

- **Environment isolation:** Terraform backend configuration and deployment controls needed to prevent cross-environment changes.
- **Workload scheduling:** Node-group separation and Kubernetes placement policies needed to reflect different resource profiles.
- **Private networking:** Worker nodes needed secure registry connectivity after moving into private subnets.
- **Workload identity:** AWS permissions needed to match the access requirements of individual workloads.
- **Capacity strategy:** Spot and On-Demand capacity needed to balance cost against availability and interruption tolerance.
- **Observability:** Resource metrics and application symptoms needed to be considered together during troubleshooting.

These concerns reinforced that reliable platform engineering requires coordination between Kubernetes, cloud infrastructure, security, and delivery workflows.

## Technologies

**Cloud and infrastructure:** AWS, Amazon EKS, EC2, VPC, IAM, Amazon ECR, Amazon RDS for PostgreSQL, Amazon S3, Terraform

**Containers and orchestration:** Docker, Kubernetes, node groups, taints and tolerations, affinity rules, Cluster Autoscaler

**Infrastructure delivery:** Terraform remote state, S3 backend, DynamoDB state locking, CI/CD approval controls

**Workload identity and networking:** IAM Roles for Service Accounts (IRSA), OIDC, VPC endpoints, security groups

**Build, security, and artifacts:** GitHub, Jenkins, Trivy, SonarQube, JFrog

**Monitoring:** Prometheus, Grafana

## What I Learned

This work reinforced the importance of connecting infrastructure design with operational safeguards.

Terraform state isolation and production approval controls protect against unintended infrastructure changes. Kubernetes node-group separation helps align workload placement and capacity with application requirements. Private-subnet deployments depend on the correct registry connectivity, and IRSA provides a more granular model for granting AWS permissions to workloads.

Across these areas, effective platform engineering means investigating failures across the full dependency chain—from Kubernetes configuration and IAM permissions to VPC networking and infrastructure automation.

*Sensitive infrastructure identifiers and configuration details have been omitted.*
