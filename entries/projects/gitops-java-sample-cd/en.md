---
title: "How can Kubernetes delivery stay traceable from CI to GitOps?"
type: project
category: "CI/CD & GitOps"
layout: case-study
lang: en
slug: gitops-java-sample-cd
permalink: /entries/gitops-java-sample-cd/
date: 2026-10-08
year: 2026
image: "/images/projects/example-project/gitops-java-sample-cd/GitOps Java Sample CD.png"
thumbnail: "/images/projects/example-project/gitops-java-sample-cd/GitOps Java Sample CD.png"
cover: "/images/projects/example-project/gitops-java-sample-cd/GitOps Java Sample CD.png"
cover_alt: "GitOps continuous delivery workflow"
thumbnail_alt: "Jenkins and Argo CD Kubernetes deployment workflow"
label: "GITOPS DEMO"
role: "CI/CD workflow development"
technologies: [Jenkins, Argo CD, Kubernetes, Docker, Maven, SonarQube, Trivy]
code: "https://github.com/SaraIravani/gitops-java-sample-cd"
demo: ""
paper: ""
excerpt: "Builds and deployments need clear responsibilities and versioned desired state. This sample separates Jenkins build stages from Kubernetes configuration and an Argo CD-oriented GitOps workflow."
---

## Overview

This project demonstrates a delivery workflow for a Java application using Jenkins, Docker, Kubernetes, and Argo CD.

It explores how CI and CD responsibilities can be separated while keeping deployment configuration in version control.

## Workflow

The documented workflow includes:

1. Source checkout
2. Maven build and test stages
3. SonarQube code-quality analysis
4. Docker image build and registry publishing
5. Kubernetes deployment configuration
6. GitOps-oriented delivery using Argo CD

Trivy is included in the project's security tooling.

## GitOps Principles

The project illustrates several GitOps concepts:

- Keep deployment manifests in version control.
- Make the desired application state visible in Git.
- Use a reconciliation-based deployment workflow.
- Separate application build steps from Kubernetes delivery configuration.

## Operational Considerations

A working end-to-end workflow depends on the Kubernetes environment, Argo CD configuration, registry access, credentials, and the exact pipeline implementation.

Production capabilities such as automated rollback, multi-cluster deployment, or progressive delivery should only be claimed when separately implemented and verified.

## Technologies

Jenkins, Argo CD, Kubernetes, Docker, Maven, SonarQube, Trivy.

## Repository

[View GitOps Java Sample CD on GitHub](https://github.com/SaraIravani/gitops-java-sample-cd)
