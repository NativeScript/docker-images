# V8 Snapshot
A minimal Docker image based on Alpine Linux (~5MB) containing only a basic shell and the dynamic dependencies of the `mksnapshot` tools. The total image size is ~13 MB.

## Installation
`$ docker pull nativescript/v8-snapshot`

## Usage
1) `$ docker run --rm -ti nativescript/v8-snapshot`

## Local Build
1) `$ cd <the-directory-of-this-readme>`
2) `$ docker build -t nativescript/v8-snapshot .`

## Publish to Docker Hub
1) follow the [`Local Build`](#local-build) section
2) `$ docker login`
3) `$ docker tag nativescript/v8-snapshot nativescript/v8-snapshot`
4) `$ docker tag nativescript/v8-snapshot nativescript/v8-snapshot:<tag>`
5) `$ docker push nativescript/v8-snapshot`

> We add an additional tag in order to set a semver version for old Runtimes support and a better history. If you skip step (4), it will publish and override only the `latest` tag.

## Snapshot NativeScript App
1) [optional] follow the [`Local Build`](#local-build) section
2) run a release build with --env.snapshot (TODO: maybe we need a flag for the Docker usage?)

## FAQ
**Question:** How did we find the required snapshot tool dependencies?

**Answer:** The `mksnapshot` tool is a dynamic program and inspired by [this blog post](http://glaudiston.blogspot.com/2015/06/how-to-make-very-very-small-docker.html) and [this SO thread](https://askubuntu.com/questions/1023962/how-to-install-libc6i386-on-16-04-4-64bit), we were able to find the required dependencies on Ubuntu@18.04. In short, after the proper environment setup, download the linux V8 snapshot tools from `https://github.com/NativeScript/mksnapshot-tools/tree/master/mksnapshot-tools/<the-current-V8-version-used-in-NativeScript>/linux-x64` and run `$ ldd mksnapshot-arm64` and `$ ldd mksnapshot-ia32`, then just copy the required libraries in the `lib` folder. You could find the currently used V8 version by looking at [this file](https://github.com/NativeScript/android-runtime/blob/release/build-artifacts/project-template-gradle/settings.json#L2) ([here's a fallback permalink](https://github.com/NativeScript/android-runtime/blob/0d908c11741079d2286e7ed61ebe95bde45e4ba5/build-artifacts/project-template-gradle/settings.json#L2)).

##
**Question:** When do we need to update these dependencies?

**Answer:** Releasing a new version of the V8 snapshot tools (this is done for each V8 update in the Runtime) could require new dependencies and an image update.

##
**Question:** Why don't we build the snapshots locally?

**Answer:** The V8 snapshot tools are not available on Windows and starting from MacOS Catalina, the 32-bit processes are not supported on MAC. In addition, on Linux, you need several native dependencies in order to run the tools. Providing this Docker image, we have a cross-platform unified snapshots generation.