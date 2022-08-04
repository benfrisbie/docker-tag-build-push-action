[![GitHub release](https://img.shields.io/github/release/docker/metadata-action.svg?style=flat-square)](https://github.com/benfrisbie/docker-tag-build-push-action/releases/latest)
[![GitHub marketplace](https://img.shields.io/badge/marketplace-docker--metadata--action-blue?logo=github&style=flat-square)](https://github.com/marketplace/actions/docker-tag-build-push)

## About
A composite action to tag, build, and optionally login and push a docker image to a registry.

This action basically just chains calls to the following well known actions with some sane defaults.
- [docker/login-action](https://github.com/docker/login-action)
- [docker/metadata-action](https://github.com/docker/metadata-action)
- [docker/build-push-action](https://github.com/docker/build-push-action)

Defaults are set to push an image named `${{ github.repository }}` to the (ghcr.io) registry on git push events `${{ github.event_name == 'push' }}` using the username:`${{ github.actor }}` and password:`${{ github.token }}`.

___

* [Usage](#usage)
    * [Basic](#basic)
    * [Push to Docker Hub](#push-to-docker-hub)

## Usage
### Basic
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
      - uses: benfrisbie/docker-tag-build-push-action/@main
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
      - uses: benfrisbie/docker-tag-build-push-action/@main
        with:
            registry: 'docker.io'
            username: ${{ secrets.DOCKERHUB_USERNAME }}
            password: ${{ secrets.DOCKERHUB_TOKEN }}
```