# helm-deploy

Deploy applications to Kubernetes clusters using Helm with automatic rollback and failure diagnostics.

## Features

- GitHub environment integration for deployment protection
- Automatic rollback on deployment failure
- Dry-run mode for previewing changes
- Failure diagnostics (pod logs, events, helm history)
- Concurrency control per environment

## Inputs

| Input | Type | Required | Default | Description |
|-------|------|----------|---------|-------------|
| `environment` | string | No | - | GitHub environment name for deployment protection |
| `environment-url` | string | No | - | URL to display in GitHub environment |
| `kubeconfig-encoding` | string | No | `base64` | Encoding of kubeconfig secret (`base64` or `none`) |
| `helm-chart-path` | string | Yes | - | Path to Helm chart directory |
| `release-name` | string | Yes | - | Helm release name |
| `release-namespace` | string | Yes | - | Kubernetes namespace for deployment |
| `values-file` | string | Yes | - | Path to Helm values file |
| `image-tag` | string | No | `''` | Image tag to deploy (defaults to `sha-<commit>`) |
| `helm-timeout` | string | No | `5m0s` | Helm deployment timeout |
| `helm-extra-args` | string | No | `''` | Additional Helm arguments |
| `app-label-selector` | string | No | `''` | Label selector for pod diagnostics (e.g., `app.kubernetes.io/name=myapp`) |
| `automatic-rollback` | boolean | No | `true` | Automatically rollback on deployment failure |
| `dry-run` | boolean | No | `false` | Preview changes without deploying |

## Secrets

| Secret | Required | Description |
|--------|----------|-------------|
| `kubeconfig` | Yes | Kubeconfig content for cluster access (base64-encoded by default) |

## Outputs

| Output | Description |
|--------|-------------|
| `release-revision` | Helm release revision number |

## Usage

```yaml
name: Deploy

on:
  push:
    branches: [main]

jobs:
  deploy:
    uses: vojtechmares/workflows/.github/workflows/helm-deploy.yml@main
    with:
      environment: production
      environment-url: https://my-app.example.com
      helm-chart-path: ./charts/my-app
      release-name: my-app
      release-namespace: production
      values-file: ./charts/my-app/values-production.yaml
      app-label-selector: app.kubernetes.io/name=my-app
    secrets:
      kubeconfig: ${{ secrets.KUBECONFIG }}
```

## Dry Run Example

Preview changes before deploying:

```yaml
jobs:
  preview:
    uses: vojtechmares/workflows/.github/workflows/helm-deploy.yml@main
    with:
      helm-chart-path: ./charts/my-app
      release-name: my-app
      release-namespace: staging
      values-file: ./charts/my-app/values-staging.yaml
      dry-run: true
    secrets:
      kubeconfig: ${{ secrets.KUBECONFIG }}
```

## Failure Diagnostics

When a deployment fails, the workflow automatically collects:

- Helm release status and history
- Current release values
- Pod events and status
- Pod logs (if `app-label-selector` is provided)
- Deployment/Job status
