# Docker Promote Tag

Used to "promote" a Docker image tag, such as promoting from "development" to
"test". Will calculate the "source" tag for a given target one. The default
mapping can be overriden, and you can always provide your own "source" to
override any mapping.

Example: the "source" for the "staging" tag (environment) is "test".

Some notes:
- Will fallback to docker hub registry (if none is provided)
- Image name will default to the current repository name
- Tag mapping is case insensitive

## Input

```yaml
inputs:
  container-registry:
    description: Container registry endpoint (fallback to Docker Hub)
    required: false
  container-registry-username:
    description: Container registry username
    required: true
  container-registry-password:
    description: Container registry password
    required: true
  image:
    description: Image name (fallback to repository name)
    required: false
  source-tag:
    description: Source (will calculate if empty)
    required: false
  target-tag:
    description: Target environment
    required: true
  tag-map:
    description: Tag map (JSON, fallback to default map)
    required: false
    default: >  # Note that this is a JSON string
      {
        "development": "development",
        "test": "development",
        "staging": "test",
        "production": "staging"
      }
```

## Output

```yaml
outputs:
  source-tag:
    description: Calculated source tag
  target-tag:
    description: Target tag (normalized)
  manifest:
    description: Image manifest (registry + image + tag)
```

## Usage

```yaml
name: Pull Request
on:
  push:
    branches: [ main ]
  workflow_dispatch:

jobs:
  promote-tag-digitalocean:
    uses: wisemen-digital/devops-ga-docker-promote-tag@main
    with:
      container-registry: ${{ vars.CONTAINER_REGISTRY_ENDPOINT }}
      container-registry-username: ${{ secrets.DIGITALOCEAN_API_USER }}
      container-registry-password: ${{ secrets.DIGITALOCEAN_API_TOKEN }}
      target-tag: staging

  promote-tag-scaleway:
    uses: wisemen-digital/devops-ga-docker-promote-tag@main
    with:
      container-registry: ${{ vars.CONTAINER_REGISTRY_ENDPOINT }}
      container-registry-username: nologin
      container-registry-password: ${{ secrets.SCALEWAY_SECRET_KEY }}
      source-tag: development
      target-tag: test
```
