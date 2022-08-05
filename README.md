[![GitHub release](https://img.shields.io/github/release/benfrisbie/docker-tag-build-push-action.svg?style=flat-square)](https://github.com/benfrisbie/docker-tag-build-push-action/releases/latest)
[![GitHub marketplace](https://img.shields.io/badge/marketplace-benfrisbie--docker--tag--build--push--action-blue?logo=github&style=flat-square)](https://github.com/marketplace/actions/docker-tag-build-push)

## About
A composite action to tag, build, and optionally login and push a docker image to a registry.

This action basically chains calls to the following popular actions with some common defaults.
- [docker/login-action](https://github.com/docker/login-action)
- [docker/metadata-action](https://github.com/docker/metadata-action)
- [docker/build-push-action](https://github.com/docker/build-push-action)
___

* [Usage](#usage)
    * [Basic](#basic)
    * [Push to Docker Hub](#push-to-docker-hub)
* [Defaults](#defaults)
* [Inputs](#inputs)

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

## Defaults
Defaults are defined to:
- Checkout the repository
- Build an image named `${{ github.repository }}:<tag>` 
- The `<tag>` is determined based on the event causing the build as defined at [docker/metadata-action#tags-input](https://github.com/docker/metadata-action#tags-input)
- Push image on git push events `${{ github.event_name == 'push' }}`
- Default registry of [ghcr.io](ghcr.io)
    - Username = `${{ github.actor }}`
    - Password = `${{ github.token }}`

## Inputs
| Name         | Type   | Description                                                                                   | Default                                                                |
|--------------|--------|-----------------------------------------------------------------------------------------------| ---------------------------------------------------------------------- |
| `checkout`   | String | Boolean ('true' or 'false') string of whether to checkout the repo at the start of the action | 'true'                                                                 |
| `push`       | String | Boolean ('true' or 'false') string of whether to push the image to the registry               | `${{ github.event_name == 'push' }}`                                   |
| `registry`   | String | Server address of Docker registry.                                                            | 'ghcr.io'                                                              |
| `username`   | String | Username used to log against the Docker registry                                              | `${{ github.actor }}`                                                  |
| `password`   | String | Password or personal access token used to log against the Docker registry                     | `${{ github.token }}`                                                  |
| `image`      | String | Docker image name to use as base                                                              | `${{ github.repository }}`                                             |
| `build-args` | List   | Newline delimited string of build-time variables                                              | ''                                                                     |
| `context`    | String | Build's context is the set of files located in the specified PATH or URL                      | [git context](https://github.com/docker/build-push-action#git-context) |
| `file`       | String | Path to the Dockerfile                                                                        | `{context}/Dockerfile`                                                 |