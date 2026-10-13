---
title: "How should EKS workloads be isolated by capacity and risk?"
type: project
category: "Cloud & Platform Engineering"
layout: case-study
lang: en
slug: eks-platform-engineering-blueprint
permalink: /entries/eks-platform-engineering-blueprint/
date: 2026-10-11
year: 2026
image: "/images/projects/example-project/eks-platform-engineering-blueprint/cover.png"
thumbnail: "/images/projects/example-project/eks-platform-engineering-blueprint/cover.png"
cover: "/images/projects/example-project/eks-platform-engineering-blueprint/cover.png"
cover_alt: "AWS EKS platform architecture blueprint"
thumbnail_alt: "AWS EKS platform engineering design"
label: "ARCHITECTURE BLUEPRINT"
role: "Platform architecture design"
technologies: [AWS, EKS, Kubernetes, Terraform, IAM, IRSA, EC2, Auto Scaling]
code: "https://github.com/SaraIravani/eks-platform-engineering-blueprint"
demo: ""
paper: ""
excerpt: "Different workloads need different capacity and availability guarantees. This architecture blueprint explores workload-specific node groups, placement controls, least-privilege access, autoscaling, and cost-aware compute choices."
---

## Overview

This project documents a production-oriented architecture for running heterogeneous workloads on Amazon EKS.

The design focuses on reliability, workload isolation, security boundaries, scalability, and infrastructure cost awareness.

## Problem

Different workload types have different resource, availability, and capacity requirements. Treating API services, batch jobs, ingestion pipelines, and system components identically can create resource contention and unnecessary infrastructure costs.

## Design Approach

The architecture explores:

- Multi-AZ EKS deployment and availability considerations
- Workload-specific node groups and capacity types
- On-Demand capacity for critical workloads and Spot capacity for interruptible workloads
- Workload placement using labels, taints, and scheduling constraints
- IAM least privilege and IAM Roles for Service Accounts (IRSA)
- Private worker-node subnets and controlled network access
- Horizontal and cluster-level autoscaling considerations
- Monitoring and operational visibility

## Key Decisions

**Workload isolation:** Separate workload classes according to resource requirements and operational criticality.

**Capacity strategy:** Use a combination of On-Demand and Spot capacity where workload interruption tolerance allows it.

**Security:** Apply least-privilege IAM and restrict unnecessary access to worker nodes and workloads.

**Availability:** Design for multiple Availability Zones while considering workload replicas, load balancing, and recovery requirements.

## Scope and Validation

This repository is an architecture blueprint. Availability targets and cost-reduction figures in the design are planning assumptions, not measured production results.

## Technologies

AWS, Amazon EKS, Kubernetes, Terraform, IAM, IRSA, EC2, Auto Scaling.

## Repository

[View the architecture blueprint on GitHub](https://github.com/SaraIravani/eks-platform-engineering-blueprint)
