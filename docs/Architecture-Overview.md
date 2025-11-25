# Mimariye Genel Bakış (Örnek)

<details>
<summary><strong>🇬🇧 English</strong></summary>

# Architecture Overview

This document dives deeper into the architecture described in the repository README. It explains how Terraform, Flux, and the supporting services assemble into a GitOps-powered BKT Platform on AWS.

## High-Level Design

The BKT Platform provisions AWS infrastructure with Terraform and runs workloads on Amazon EKS. FluxCD watches Git for desired state and reconciles Helm releases for both platform add-ons and applications. CI/CD is anchored in GitHub Actions, which builds and pushes container images to Amazon ECR before Flux applies updates in the cluster.

## Infrastructure Layer (Terraform)

- Amazon VPC with segmented public and private subnets to isolate traffic.
- Security groups and IAM roles, including GitHub OIDC trust relationships for CI/CD without static AWS keys.
- Amazon EKS control plane with managed node groups sized for the dev environment.
- Application Load Balancer managed by AWS Load Balancer Controller, backed by ACM certificates for TLS.
- Amazon Route 53 records that map friendly hostnames to the ALB.
- Amazon ECR repositories for platform and application container images.
- MongoDB Atlas resources managed externally but provisioned through Terraform modules for consistent infrastructure state.

## Kubernetes and GitOps Layer

- FluxCD bootstraps into the cluster, watches Git repositories, and applies Kustomizations and HelmReleases.
- Helm charts codify deployments for both the platform services and sample applications.
- Namespace and RBAC conventions align with Terraform outputs to keep AWS and Kubernetes resources in sync.

## Platform Add-ons (Helm)

- Vault with Kubernetes auth and Agent Injector to distribute secrets to workloads securely.
- OpenSearch/Elasticsearch paired with Kibana for centralized log collection and visualization.
- Grafana dashboards connected to Elasticsearch to surface application and platform metrics.
- Authentik as the identity provider delivering SSO across platform tools.
- Headlamp for a lightweight Kubernetes user interface tailored to developers.
- MySQL deployed via Helm chart for workloads requiring relational storage.
- Optional Fluent Bit forwarding to push pod logs into the OpenSearch stack.

## CI/CD Flow

1. Developers push application code or Helm overrides to GitHub.
2. GitHub Actions executes tests, builds container images, and pushes them to Amazon ECR.
3. The pipeline updates manifests or Helm values in Git to reference the new image version.
4. Flux detects repository changes and reconciles the cluster to the new desired state.

## Networking and Security

- AWS Load Balancer Controller provisions ALBs and attaches ACM-issued certificates for TLS.
- Route 53 maintains DNS records that route traffic to the ALB.
- Network policies and security groups enforce least privilege between services.
- Vault policies define which Kubernetes service accounts can access specific secrets.
- GitHub Actions assumes AWS roles via OIDC, eliminating long-lived credentials in pipelines.

## Observability and Operations

- OpenSearch/Elasticsearch indexes logs collected from workloads or Fluent Bit.
- Kibana offers log search and dashboards for troubleshooting.
- Grafana aggregates metrics and logs, providing a central monitoring interface.
- Single-node defaults keep the stack lightweight for development, with room to scale in production.

## Identity and Developer Experience

- Authentik delivers SSO across the platform, reducing account sprawl.
- Headlamp provides an accessible Kubernetes dashboard for developers without kubectl proficiency.
- Vault Agent Injector simplifies secrets retrieval by auto-injecting them into pods at runtime.

## Data and Secrets Flow

- Application pods authenticate to Vault via Kubernetes auth roles and receive secrets through the Agent Injector.
- Logs flow from pods into OpenSearch/Elasticsearch and surface in Kibana and Grafana.
- Metrics and dashboards in Grafana give teams feedback on platform health.
- MongoDB Atlas and the in-cluster MySQL chart support application data needs depending on the workload profile.