---
title: "How can AWS application tiers be isolated and maintained safely?"
type: project
category: "Cloud & Platform Engineering"
layout: case-study
lang: en
slug: terraform-3tier-app
permalink: /entries/terraform-3tier-app/
date: 2026-10-10
year: 2026
image: "/images/projects/example-project/terraform-3tier-app/Terraform 3-Tier.png"
thumbnail: "/images/projects/example-project/terraform-3tier-app/Terraform 3-Tier.png"
cover: "/images/projects/example-project/terraform-3tier-app/Terraform 3-Tier.png"
cover_alt: "Three-tier AWS infrastructure architecture"
thumbnail_alt: "Terraform modular AWS architecture design"
label: "INFRASTRUCTURE DESIGN"
role: "Terraform infrastructure design"
technologies: [Terraform, AWS, VPC, EC2, ALB, RDS, IAM, KMS, CloudWatch]
code: "https://github.com/SaraIravani/terraform-3tier-app"
demo: ""
paper: ""
excerpt: "A three-tier design needs clear network boundaries and controlled database access. This Terraform project separates web, application, and data layers into reusable infrastructure modules."
---

## Overview

This repository presents a modular Terraform design for a three-tier application architecture on AWS.

The design separates networking, compute, database, security, monitoring, and logging concerns into reusable infrastructure modules.

## Architecture

The proposed architecture contains three main layers:

- **Web tier:** Application Load Balancer and web-facing compute resources
- **Application tier:** Application compute resources in private subnets
- **Database tier:** RDS resources isolated in private subnets

Supporting components include VPC networking, IAM, security groups, encryption, logging, and monitoring.

## Design Goals

### Infrastructure as Code

Use reusable Terraform modules, variables, outputs, and environment-specific configuration to reduce duplication and improve maintainability.

### Network Segmentation

Separate public-facing resources from private application and database resources.

### Security

The design incorporates:

- IAM roles and least-privilege access
- Security-group-based traffic restrictions
- AWS Systems Manager Session Manager for administrative access
- AWS Secrets Manager and KMS
- Encryption and centralized logging considerations

### Operations

CloudWatch monitoring, alarms, and logging services are included in the proposed design.

## Key Decisions

The architecture aims to balance security, operational complexity, scalability, and infrastructure cost.

Trade-offs such as NAT Gateway placement, autoscaling configuration, and environment separation should be evaluated against actual workload and budget requirements.

## Scope and Validation

This repository documents an infrastructure design. Its architecture and configuration should not be interpreted as evidence of a deployed production environment or measured performance results.

## Technologies

Terraform, AWS VPC, EC2, Application Load Balancer, Auto Scaling, RDS, IAM, KMS, Secrets Manager, CloudWatch.

## Repository

[View the Terraform project on GitHub](https://github.com/SaraIravani/terraform-3tier-app)
