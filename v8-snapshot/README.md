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
3) `$ docker tag nativescript/v8-snapshot nativescript/v8-snapshot` (**IMPORTANT**: this will update the **latest** tag and the users will get the new version. Do not publish *breaking changes* in this tag!)
4) `$ docker tag nativescript/v8-snapshot nativescript/v8-snapshot:<tag>` 
5) `$ docker push nativescript/v8-snapshot`

> We add the additional semver tag for a better history and local testing. If you skip step (4), it will publish and override only the `latest` tag. **IMPORTANT**: When we update the **latest** tag, all users will get the new version. Do not publish *breaking changes* in this tag!

## Snapshot NativeScript App
1) [optional] follow the [`Local Build`](#local-build) section
2) run a release build with --env.snapshot and --env.snapshotInDocker

## FAQ
**Question:** How did we find the required snapshot tool dependencies (the content of `lib` and `lib64`)?

**Answer:** The `mksnapshot` tool is a dynamic program and inspired by [this blog post](http://glaudiston.blogspot.com/2015/06/how-to-make-very-very-small-docker.html) and [this SO thread](https://askubuntu.com/questions/1023962/how-to-install-libc6i386-on-16-04-4-64bit), we were able to find the required dependencies on Ubuntu@18.04. In short, after the proper environment setup, download the linux V8 snapshot tools from `https://github.com/NativeScript/mksnapshot-tools/tree/master/mksnapshot-tools/<the-current-V8-version-used-in-NativeScript>/linux-x64` and run `$ ldd mksnapshot-arm64` and `$ ldd mksnapshot-ia32`, then just copy the required libraries in the `lib` folder. You could find the currently used V8 version by looking at [this file](https://github.com/NativeScript/android-runtime/blob/release/build-artifacts/project-template-gradle/settings.json#L2) ([here's a fallback permalink](https://github.com/NativeScript/android-runtime/blob/0d908c11741079d2286e7ed61ebe95bde45e4ba5/build-artifacts/project-template-gradle/settings.json#L2)).

##
**Question:** When do we need to update these dependencies?

**Answer:** Releasing a new version of the V8 snapshot tools (this is done for each V8 update in the Runtime) could require new dependencies and an image update.

##
**Question:** What is bin/xxd?

**Answer:** xxd is a tool for hexdumps for optimizing the app size in combination with Android ABI split. It's copied from the vim package (https://github.com/vim/vim) along with it's license file. 

##
**Question:** Why don't we build the snapshots locally?

**Answer:** The V8 snapshot tools are not available on Windows and starting from MacOS Catalina, the 32-bit processes are not supported on MAC. In addition, on Linux, you need several native dependencies in order to run the tools. Providing this Docker image, we have a cross-platform unified snapshots generation.
