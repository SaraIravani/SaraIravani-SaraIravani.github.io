---
title: "How can Linux security settings be applied consistently?"
type: project
category: "Security & DevSecOps"
layout: case-study
lang: en
slug: harden-linux
permalink: /entries/harden-linux/
date: 2026-10-07
year: 2026
image: "/images/projects/example-project/harden-linux/Linux Security Hardening.png"
thumbnail: "/images/projects/example-project/harden-linux/Linux Security Hardening.png"
cover: "/images/projects/example-project/harden-linux/Linux Security Hardening.png"
cover_alt: "Linux security hardening and automation"
thumbnail_alt: "Ansible-based Linux security hardening"
label: "SECURITY AUTOMATION"
role: "Ansible playbook development"
technologies: [Ansible, Linux, SSH, Fail2Ban, Prometheus, Grafana]
code: "https://github.com/SaraIravani/harden-linux"
demo: ""
paper: ""
excerpt: "Manual hardening is difficult to repeat consistently. This role-based Ansible project organizes access controls, system settings, auditing, and related security tasks into reusable playbooks."
---

## Overview

This project organizes Linux security hardening tasks into Ansible playbooks and reusable roles.

The goal is to make common security configuration tasks more consistent and easier to maintain across multiple Linux systems.

## Security Areas

The repository covers areas including:

- Initial system assessment
- Security updates and base configuration
- User and access management
- Password and file-permission policies
- Network security
- Service configuration
- Kernel hardening
- System auditing
- Fail2Ban-based protection
- Monitoring with Prometheus and Grafana

## Design Approach

### Role-Based Structure

Separate security concerns into focused roles instead of keeping all tasks in one large playbook.

### Idempotent Configuration

Prefer Ansible modules and declarative configuration so tasks can be re-applied predictably.

### Ordered Execution

Run prerequisite configuration before dependent tasks, such as installing required packages before configuring their services.

### Verification

Review playbook output and validate the resulting system configuration after execution. Apply the playbooks to a test environment before using them on important systems.

## Operational Considerations

The impact of hardening depends on the target Linux distribution, current configuration, privileges, and enabled services. Changes should be tested before applying them to production hosts.

## Technologies

Ansible, Linux, SSH, Fail2Ban, Prometheus, Grafana.

## Repository

[View Harden Linux on GitHub](https://github.com/SaraIravani/harden-linux)
