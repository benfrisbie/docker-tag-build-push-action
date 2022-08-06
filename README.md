[![GitHub release](https://img.shields.io/github/release/benfrisbie/docker-tag-build-push-action.svg?style=flat-square)](https://github.com/benfrisbie/docker-tag-build-push-action/releases/latest)
[![GitHub marketplace](https://img.shields.io/badge/marketplace-benfrisbie--docker--tag--build--push--action-blue?logo=github&style=flat-square)](https://github.com/marketplace/actions/docker-tag-build-push)
[![CI workflow](https://img.shields.io/github/workflow/status/benfrisbie/docker-tag-build-push-action/ci?label=ci&logo=github&style=flat-square)](https://github.com/benfrisbie/docker-tag-build-push-action/actions?workflow=ci)

## About
A composite action to tag, build, and optionally login and push a docker image to a registry.

This action basically chains calls to the following popular actions with some common defaults.
- [docker/login-action](https://github.com/docker/login-action)
- [docker/setup-qemu-action](https://github.com/docker/setup-qemu-action)
- [docker/setup-buildx-action](https://github.com/docker/setup-buildx-action)
- [docker/metadata-action](https://github.com/docker/metadata-action)
- [docker/build-push-action](https://github.com/docker/build-push-action)
___

* [Usage](#usage)
    * [Basic](#basic)
    * [Push to Docker Hub](#push-to-docker-hub)
    * [Push to Multiple Registries](#push-to-multiple-registries)
* [Inputs](#inputs)
    * [Defaults](#defaults)
* [Outputs](#outputs)

## Usage
### Basic
Push to ghcr.io by default
```yaml
name: Docker

on:
  push:
    branches:
      - main
    tags:
      - 'v*.*.*'
  pull_request:

jobs:
  docker:
    runs-on: ubuntu-latest
    steps:
      - uses: benfrisbie/docker-tag-build-push-action/@v1
```

### Push to Docker Hub
```yaml
name: Docker

on:
  push:
    branches:
      - main
    tags:
      - 'v*.*.*'
  pull_request:

jobs:
  docker:
    runs-on: ubuntu-latest
    steps:
      - uses: benfrisbie/docker-tag-build-push-action/@v1
        with:
            images: docker.io/${{ github.repository }}
            registry: 'docker.io'
            username: ${{ secrets.DOCKERHUB_USERNAME }}
            password: ${{ secrets.DOCKERHUB_TOKEN }}
```

### Push to Multiple Registries
Push to Docker Hub and ghcr.io. First login to docker.io and the automatically login to ghcr.io by default.
```yaml
name: Docker

on:
  push:
    branches:
      - main
    tags:
      - 'v*.*.*'
  pull_request:

jobs:
  docker:
    runs-on: ubuntu-latest
    steps:
      - uses: docker/login-action@v2
        with:
            registry: 'docker.io'
            username: ${{ secrets.DOCKERHUB_USERNAME }}
            password: ${{ secrets.DOCKERHUB_TOKEN }}
      - uses: benfrisbie/docker-tag-build-push-action/@v1
        with:
            images: |
                docker.io/${{ github.repository }}
                ghcr.io/${{ github.repository }}
```

## Inputs
The inputs are the same as the ones defined in:
- [docker/login-action#inputs](https://github.com/docker/login-action#inputs)
- [docker/metadata-action#inputs](https://github.com/docker/metadata-action#inputs)
- [docker/build-push-action#inputs](https://github.com/docker/build-push-action#inputs)

### Defaults
In an attempt to make this action easier to use, the following defaults are set:
- Build an image named `ghcr.io/${{ github.repository }}` 
- The image tags are determined based on the event causing the build as defined at [docker/metadata-action#tags-input](https://github.com/docker/metadata-action#tags-input)
- Push image to registry on git push events `${{ github.event_name == 'push' }}`
- Default registry of [ghcr.io](ghcr.io)
    - Username = `${{ github.actor }}`
    - Password = `${{ github.token }}`

## Outputs
The outputs are the same as the ones defined in:
- [docker/metadata-action#outputs](https://github.com/docker/metadata-action#outputs)
- [docker/build-push-action#outputs](https://github.com/docker/build-push-action#outputs)
