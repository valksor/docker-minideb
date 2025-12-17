# Fork Notice

[![valksor](https://badgen.net/static/org/valksor/green)](https://github.com/valksor)  

This is a fork of [bitnami/minideb](https://github.com/bitnami/minideb).

- **For upstream issues/PRs**: Please submit to the original bitnami/minideb repository
- **For fork-specific issues**: Only open issues here if related to valksor/minideb customizations
- **Image**: `ghcr.io/valksor/minideb` (GitHub Container Registry)
- **Architecture**: ARM64 only, optimized for self-hosted runners

---

<p align="center">
    <img width="400px" height=auto src="https://dyltqmyl993wv.cloudfront.net/bitnami/bitnami-by-vmware.png" />
</p>

<p align="center">
    <a href="https://github.com/bitnami/minideb/actions/workflows/cd.yml"><img src="https://github.com/bitnami/minideb/actions/workflows/cd.yml/badge.svg?branch=master" /></a>
    <a href="https://hub.docker.com/r/bitnami/minideb/"><img src="https://badgen.net/docker/pulls/bitnami/minideb?icon=docker&label=pulls" /></a>
    <a href="https://hub.docker.com/r/bitnami/minideb/"><img src="https://badgen.net/docker/stars/bitnami/minideb?icon=docker&label=stars" /></a>
    <a href="https://github.com/bitnami/minideb"><img src="https://badgen.net/github/forks/bitnami/minideb?icon=github&color=grey" /></a>
    <a href="https://github.com/bitnami/minideb"><img src="https://badgen.net/github/stars/bitnami/minideb?icon=github&color=grey" /></a>
    <a href="https://twitter.com/bitnami"><img src="https://badgen.net/badge/twitter/@bitnami/1DA1F2?icon&label" /></a>
</p>

# What is Minideb
A minimalist Debian-based image built specifically to be used as a base image for containers.

# Use Minideb
You can use the image directly, e.g.
```
$ docker run --rm -it ghcr.io/valksor/minideb:latest
```

There are tags for the different Debian releases. The default is sid (Debian unstable).
```
$ docker run --rm -it ghcr.io/valksor/minideb:latest
$ docker run --rm -it ghcr.io/valksor/minideb:sid
$ docker run --rm -it ghcr.io/valksor/minideb:trixie
```

The images are built on push to master and have the security release enabled, so will contain any security updates released more than 24 hours ago.

You can also use the images as a base for your own `Dockerfile`:
```
FROM ghcr.io/valksor/minideb:latest
# or explicitly:
FROM ghcr.io/valksor/minideb:sid
```

# Why use Minideb
  * This image aims to strike a good balance between having small images and having many quality packages available for easy integration.
  * The image is based on glibc for wide compatibility and is using apt for access to a large number of packages. To reduce the size of the image, some things that aren't required in containers are removed:
    * Packages that aren't often used in containers (hardware-related, init systems, etc.)
    * Some files that aren't usually required (docs, man pages, locales, caches)
  * These images also include an `install_packages` command that you can use instead of apt. This takes care of some things for you:
    * Install the named packages, skipping prompts, etc.
    * Clean up the apt metadata afterward to keep the image small.
    * Retrying if apt fails. Sometimes a package will fail to download due to a network issue, and this may fix that, which is particularly useful in an automated build pipeline.

    For example:
    ```
    $ install_packages apache2 memcached
    ```

# Adoption of Minideb
The minideb container image is the base image for many Bitnami-maintained language runtimes including [php](https://github.com/bitnami/containers/tree/main/bitnami/php-fpm), [nodejs](https://github.com/bitnami/containers/tree/main/bitnami/node), [ruby](https://github.com/bitnami/containers/tree/main/bitnami/ruby) and infrastructure components including [mariadb](https://github.com/bitnami/containers/tree/main/bitnami/mariadb), [redis](https://github.com/bitnami/containers/tree/main/bitnami/redis), [nginx](https://github.com/bitnami/containers/tree/main/bitnami/nginx) and [mongodb](https://github.com/bitnami/containers/tree/main/bitnami/mongodb).

# Compatibility
The image points to the Debian archive, so you are free to install the packages from there that you need. However, because some `Essential` packages have been removed they may not always install or work correctly.

In those cases, you can figure out which package is needed and manually specify to install it along with your desired packages. Please feel free to submit an issue request so that we can reach out and help you quickly.

# Security
Minideb is based on Debian and relies on their security updates. The images are built daily and have the security release enabled, so will contain any security updates released more than 24 hours ago.

Note that Debian [does not fix every CVE that affects their packages](https://www.debian.org/security/faq#cvedsa), which means that CVE scanners may detect unfixed vulnerabilities in Minideb images. In those cases, you can check the [Debian security tracker](https://security-tracker.debian.org/tracker/) to see whether Debian intends to release an update to fix it.

To keep compatibility with Debian, we will not patch any vulnerabilities in Minideb directly. If Debian does not fix the CVE then it will also remain in Minideb. If you find a vulnerability that is fixed in Debian but not in the latest images of Minideb then please file an issue as that is not intentional.

On [this page](https://docs.bitnami.com/kubernetes/open-cve-policy/), you can find more information about the Bitnami policy regarding CVEs. In the same way, if you find a security issue with how the Minideb images are built or published then please report it to us.

# Building Minideb
We provide a Makefile to help you build Minideb locally. It should be run on a Debian-based machine and requires sudo privileges.
```
$ sudo make
```

To build an individual release (trixie, or sid)
```
$ sudo make sid
```

To test the resulting image:
```
$ sudo make test-sid
```

Building the image with podman instead of docker is possible, if you replace docker with podman in 4 scripts:
```
$ sed -i "s/docker /podman /g" buildone dockerdiff import test
```

## Multi-arch Configuration (Fork Specific)

This fork publishes multi-arch images for `linux/amd64` and `linux/arm64`. CI builds each arch natively on its own runner and then assembles a single multi-arch manifest list per tag (`:sid`, `:trixie`, `:latest`), so consumers can `FROM ghcr.io/valksor/minideb:sid` from any host architecture.

### Runners used by CI

- **arm64**: a self-hosted runner with the `self-arm` label (running on ARM64 hardware — Raspberry Pi 4/5, AWS Graviton, etc.).
- **amd64**: GitHub-hosted `ubuntu-24.04` (no extra setup required).

To set up the arm64 self-hosted runner:

1. **Set up ARM64 hardware**
2. **Install Docker**:
   ```bash
   curl -fsSL https://get.docker.com -o get-docker.sh
   sudo sh get-docker.sh
   sudo usermod -aG docker $USER
   ```
3. **Install GitHub Actions runner** with the `self-arm` label:
   ```bash
   ./config.sh --labels self-arm
   sudo ./svc.sh install
   sudo ./svc.sh start
   ```

### Building images locally

`buildone` and `buildall` accept an architecture argument:

```bash
# Build a single dist for a single arch
sudo ./buildone trixie arm64
sudo ./buildone sid amd64

# Build all dists × arches (defaults to "amd64 arm64")
sudo ./buildall

# Restrict to one arch
ARCHS="arm64" sudo ./buildall
```

Cross-arch local builds require QEMU/binfmt on the host. Native-arch local builds need no extra setup.

The `make trixie` / `make sid` shortcuts call `mkimage` directly and default to `ARCH=arm64`; pass `ARCH=amd64 sudo make trixie` to override.


# Contributing
We'd love for you to contribute to this image. You can request new features by creating an [issue](https://github.com/bitnami/minideb/issues), or submit a [pull request](https://github.com/bitnami/minideb/pulls) with your contribution.

# License

Copyright &copy; 2025 Broadcom. The term "Broadcom" refers to Broadcom Inc. and/or its subsidiaries.

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.
