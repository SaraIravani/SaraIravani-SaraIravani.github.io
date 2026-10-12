---
title: "Secure CI Challenge"
type: project
category: "Security & DevSecOps"
layout: case-study
lang: en
slug: secure-ci-challenge
permalink: /entries/secure-ci-challenge/
date: 2026-10-06
year: 2026
image: "/images/projects/example-project/secure-ci-challenge/Secure CI Challenge.png"
thumbnail: "/images/projects/example-project/secure-ci-challenge/Secure CI Challenge.png"
cover: "/images/projects/example-project/secure-ci-challenge/Secure CI Challenge.png"
cover_alt: "DevSecOps security scanning pipeline"
thumbnail_alt: "GitHub Actions with Semgrep and Trivy"
label: "DEVSECOPS LAB"
role: "Security scanning workflow development"
technologies: [GitHub Actions, Semgrep, Trivy, Docker, Node.js]
code: "https://github.com/SaraIravani/secure-ci-challenge"
demo: ""
paper: ""
excerpt: "A hands-on DevSecOps lab that integrates Semgrep static analysis and Trivy container scanning into a GitHub Actions workflow."
---

## Overview

Secure CI Challenge demonstrates how security scanning can be incorporated into an automated CI workflow for a Node.js application packaged with Docker.

## Security Checks

The project uses:

- **Semgrep:** Static analysis for selected insecure coding patterns
- **Trivy:** Container image vulnerability scanning
- **GitHub Actions:** Automated execution of checks in the CI workflow

## Intentional Vulnerabilities

The sample application intentionally contains insecure patterns for educational purposes, including hard-coded secrets, dynamic code evaluation, command-injection risks, and insufficient access controls around sensitive file access.

These examples help demonstrate how security tools can identify potential issues during development.

## Workflow

The intended workflow runs security checks automatically on relevant repository events, such as pushes or pull requests, depending on the configured GitHub Actions triggers.

## Learning Objectives

- Introduce static analysis into CI.
- Scan container images for known vulnerabilities.
- Make security findings visible earlier in the development lifecycle.
- Understand why a successful pipeline alone does not guarantee application security.

## Safety Note

This is an educational lab, not a production-ready application. Its intentional vulnerabilities should remain confined to a controlled learning environment.

## Technologies

GitHub Actions, Semgrep, Trivy, Docker, Node.js.

## Repository

[View Secure CI Challenge on GitHub](https://github.com/SaraIravani/secure-ci-challenge)
