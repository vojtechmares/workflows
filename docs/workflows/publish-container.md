# publish-container

Build, publish, and sign container images to GitHub Container Registry (GHCR).

## Features

- Automatic image tagging (branch, PR, SHA, latest)
- Provenance attestation and SBOM generation
- Cosign keyless signing via GitHub OIDC
- Registry-based build caching

## Inputs

| Input | Type | Required | Description |
|-------|------|----------|-------------|
| `image-name` | string | Yes | Image name within GHCR |

## Secrets

| Secret | Required | Description |
|--------|----------|-------------|
| `build_secrets` | No | Secrets to pass to Docker build |

## Outputs

| Output | Description |
|--------|-------------|
| `images` | Full image URLs including tags |

## Usage

```yaml
name: Build and Publish

on:
  push:
    branches: [main]
  pull_request:

jobs:
  publish:
    uses: vojtechmares/workflows/.github/workflows/publish-container.yml@main
    with:
      image-name: my-app
    secrets:
      build_secrets: |
        npm_token=${{ secrets.NPM_TOKEN }}
```

## Image Tags

The workflow automatically generates tags based on the event:

| Event | Tag Format |
|-------|------------|
| Push to branch | `<branch>` |
| Pull request | `pr-<number>` |
| Any commit | `<branch>-<sha>` |
| Push to default branch | `latest` |
