# 🗿 Static

Build things statically with the magic of [musl](https://www.musl-libc.org/).

## Usage

Enter a directory and run:

```shell
# Build for current CPU architecture.
$ docker buildx build --output output .

# Build for multiple architectures (AMD64 & ARM64).
$ docker buildx build --platform linux/amd64,linux/arm64 --output output .
```

Then find the programs in the `output` directory.

Optional build arguments:

- `PREFIX`: The `--prefix` option for `configure` command.
- `PROXY`: The `http_proxy` & `https_proxy` environment variable while building.

## Q&A

**What is the builder?**

An image with built musl libc library and common build tools.

It's built with `.musl/Dockerfile` and uploaded to [moycat/musl](https://hub.docker.com/r/moycat/musl) to save time.

**Having problem building for other architectures?**

Firstly you need a Docker version with [buildx](https://docs.docker.com/buildx/working-with-buildx/) support.

Then you have to create a builder for multi-platform building.

If you are working on a native Linux, try running:

```shell
$ docker run --rm --privileged multiarch/qemu-user-static --reset -p yes
```
