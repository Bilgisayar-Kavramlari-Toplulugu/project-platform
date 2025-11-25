# Project Definition

<details>
<summary><strong>🇬🇧 English</strong></summary>

## Overview

The BKT Platform demonstrates how to run a GitOps-enabled developer platform on AWS. Terraform provisions the cloud foundation, FluxCD keeps Amazon EKS in sync with Git, and GitHub Actions automates build and release pipelines. This document expands on the README by detailing the project’s motivation, expected value, and delivery boundaries.

## Purpose

- Provide a reusable reference implementation for GitOps-driven platform operations on AWS.
- Streamline developer onboarding by standardizing infrastructure, deployment, secrets management, and observability.
- Eliminate manual configuration drift through continuous reconciliation of infrastructure (Terraform) and workloads (Flux).

## Why It Matters

- **Consistency:** Infrastructure and application state live in version control, enabling reproducible environments.
- **Security:** GitHub Actions authenticates via OIDC, Vault distributes secrets at runtime, and ACM enforces TLS across ingress traffic.
- **Speed:** Automated builds, image publishing to Amazon ECR, and Flux-driven deployments reduce lead time from commit to cluster.
- **Visibility:** OpenSearch, Kibana, and Grafana give teams immediate insights into logs and metrics without extra setup.

## Key Objectives

1. Provision AWS networking, compute, and supporting services with Terraform modules (VPC, subnets, security groups, EKS, ALB, Route 53, ACM, ECR, IAM with GitHub OIDC, MongoDB Atlas).
2. Bootstrap FluxCD to manage platform services and sample applications through Helm releases and Kustomizations.
3. Deliver a CI/CD pipeline using GitHub Actions that builds, tests, and pushes container images plus updates manifests.
4. Enable secure operations with Vault, enforce least-privilege access, and give developers SSO and UI access via Authentik and Headlamp.

## Value Propositions

- **Platform Teams:** Gain a blueprint for automating infrastructure and platform add-ons without bespoke scripts.
- **Application Developers:** Interact through Git commits and Helm overrides while benefiting from prewired secrets, databases, and dashboards.
- **Security & Compliance:** Leverage ephemeral credentials, TLS everywhere, and auditable Git history for configuration changes.
- **Operations:** Use observability tooling out of the box and rely on Git as the single source of truth for rollbacks and disaster recovery.

## Scope

### In Scope

- Terraform modules for core AWS resources (VPC, subnets, security groups, ALB, Route 53, ACM, EKS node groups, ECR, IAM with GitHub OIDC).
- Terraform-managed MongoDB Atlas integration.
- Flux bootstrap, GitOps repository structure, and Helm releases for Vault, OpenSearch/Elasticsearch + Kibana, Grafana, Authentik, Headlamp, MySQL, and optional Fluent Bit.
- Sample application Helm chart including Deployment, Service, Ingress, ConfigMap, and Vault-driven secrets wiring.
- GitHub Actions workflows for build, test, image push, and manifest updates.
- Route 53 DNS entries and ACM certificates delivering HTTPS endpoints fronted by AWS Load Balancer Controller.

### Out of Scope

- Multi-cloud or hybrid-cloud support.
- Advanced rollout patterns (blue/green, canary) beyond standard Flux deployments.
- Production-grade high availability, auto-scaling policies, and cost-optimization practices.
- Regulated compliance frameworks (PCI, SOC2) or hardening beyond development defaults.

## Success Criteria

- Terraform plans and applies execute without manual post-configuration steps.
- Flux reconciles platform and application changes from Git within minutes of merge.
- Developers can modify the sample Helm chart and see the update live through the managed HTTPS endpoint.
- Observability dashboards display workload metrics and logs without additional configuration.

## Stakeholders

- **Project Lead:** @flovearth
- **Platform Engineering:** Owns Terraform modules, Flux configuration, and platform add-ons.
- **Application Teams:** Consume the platform, contribute Helm overrides, and manage service lifecycles.
- **Security & Ops:** Review IAM policies, Vault roles, and monitor observability outputs.

## Related Documents

- [Architecture Overview](Architecture-Overview.md)
- [Development Workflow](Development-Workflow.md)
- [Team Information](Team.md)

</details>