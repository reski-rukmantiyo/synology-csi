# Build

## Build package

```bash
make
```

## Build Docker Image

```bash
# e.g. docker build -t jparklab/synology-csi .
docker build [-f Dockerfile] -t <repo>[:<tag>] .
```

## Build Docker Multi-architecture Images

In order to build a multiarch image, you must have Docker 19.03 or higher version that supports [buildx](https://docs.docker.com/buildx/working-with-buildx/)

```bash
# e.g. ./build.sh -t jparklab/synology-csi
./build.sh -t <repo>[:<tag>] .