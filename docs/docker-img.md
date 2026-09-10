# Redrose Linux Docker Image

## Download

```bash
docker pull ghcr.io/redroselinux/redroselinux:latest
```

## Build

### Cloning the repo

Follow the instructions in [the ISO building docs](/build-your-own-image.md).

### Building

Use the `docker` make target:

```bash
make docker
```

If your `docker` needs `sudo` / `doas` / whatever, use:

```bash
make docker DOCKER_PRE_CMD="sudo" # or whatever you wish to use
```

## Setup

The base image comes with Busybox utils; you may want to install the coreutils packages that the installer would normally give you.

### GNU coreutils/findutils

```bash
car install coreutils findutils
```

### uutils coreutils/findutils

```bash
car install uu-coreutils uu-findutils
```
