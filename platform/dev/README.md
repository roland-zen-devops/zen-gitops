# Development platform prerequisites

This directory contains the development-only resources that must exist before
the Argo CD applications are enabled.

## Ownership

- Terraform owns EKS, ECR, RDS, IAM roles, and AWS Secrets Manager values.
- Helm owns the AWS Load Balancer Controller, External Secrets Operator, Argo CD,
  and Metrics Server installations.
- This repository owns the `ClusterSecretStore`, `ExternalSecret` mappings, and
  application workloads reconciled by Argo CD.

## Secret flow

1. Terraform stores `/pharma/dev/db-credentials` and `/pharma/dev/jwt-secret` in
   AWS Secrets Manager.
2. External Secrets Operator authenticates through IRSA using the
   `external-secrets/external-secrets` service account.
3. `external-secrets.yaml` creates the `db-credentials` and `jwt-secret`
   Kubernetes Secrets in the `dev` namespace.
4. Workloads consume only the required secret with `envFrom`; secret values are
   never committed to Git.

Apply this file only after the External Secrets Operator CRDs and controller are
ready. Confirm both `ExternalSecret` resources report `Ready=True` before
creating the Argo CD applications.
