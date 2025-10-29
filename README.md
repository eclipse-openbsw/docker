# Eclipse OpenBSW - Docker

This repository contains the official docker images used in the Eclipse OpenBSW project. The images
are build automatically and pushed to the GitHub Container Registry for each release. Both the
developer as well as the CI in the OpenBSW project then utilize the pre-build docker images to
ensure a defined environment and reproducibility of CI builds.

## Building the Image locally

In case you want to build the docker image locally e.g. when modifying the `Dockerfile` you can use
the provided docker-compose file and call `docker compose build <service>`. This will build the
image locally and tag it as `<service>:test`.

## Remarks

### User ID handling

A typical issue we encountered in the past when using pre-build docker images is that the `UID` of
the user inside of the image does not match to the user on the host machine. To avoid the need to
re-build the image locally with a matching `UID` we utilize a different approach. When the user
wants to start a container we overwrite the `UID` of the user in the container by mounting a custom
`/etc/passwd` file. To make this easy to use, we typically provide a docker-compose file with this
as shown below.

```yaml
configs:
  passwd:
    content: |
      build::${DOCKER_UID:-1000}:${DOCKER_GID:-1000}::/home/build:/usr/bin/bash
  group:
    content: |
      build::${DOCKER_GID:-1000}:
  shadow:
    content: "#empty"
```
