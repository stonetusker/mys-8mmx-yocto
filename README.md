# Yocto Build with Docker: MYS-8MMX-V2 (MYIR Board)

> Containerized Yocto 3.0 build system for MYIR MYS-8MMX-V2 board, integrated with Github Action.

---

## Note  
This guide is **for the MYS-8MMX-V2 board**, based on **Yocto 3.0 (Zeus)**.  
**Ensure you are using the V2 hardware revision.**

---

## 1. Host System Requirements

- **OS:** Ubuntu (latest recommended)
- **Storage:** 200 GB
- **RAM:** 12 GB or more
- **CPU:** 4+ cores

---

## 2. Install Docker CE

```
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io -y
```

---

## 3. Prepare Docker Image (Ubuntu 18.04 Base)

Create a `Dockerfile`:

```
FROM ubuntu:18.04

ENV DEBIAN_FRONTEND=noninteractive

# Essential tools
RUN apt-get update && \
    apt-get install -y locales sudo tzdata wget curl git vim nano && \
    echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    locale-gen

# Yocto dependencies
RUN apt-get install -y \
    gawk wget git-core diffstat unzip texinfo gcc \
    build-essential chrpath socat cpio python3 \
    python3-pip python3-pexpect xz-utils debianutils \
    iputils-ping libsdl1.2-dev xterm zstd psmisc

# Additional utilities
RUN apt-get install -y \
    lzop bc rsync u-boot-tools liblz4-tool screen \
    dos2unix pigz net-tools bzip2 file libssl-dev \
    libtool libtool-bin tree && \
    rm -rf /var/lib/apt/lists/*

# Add Yocto build user
RUN groupadd -g 1000 build && \
    useradd -m -u 1000 -g build -s /bin/bash build && \
    echo "build ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

USER build
WORKDIR /home/build

RUN git config --global user.name "Yocto Stonetusker Builder" && \
    git config --global user.email "contact@stonetusker.com"

SHELL ["/bin/bash", "-c"]
```

---

## 4. Build Docker Image

```
sudo docker build -t yocto-30-image .
```

---

## 5. Create Workspace & Start Container

```
mkdir -p ~/yocto-workspace
chmod 777 ~/yocto-workspace

sudo docker run -it \
  -v ~/yocto-workspace:/home/build/work \
  --name yocto-30-builder \
  yocto-30-image
```

---

## 6. (Inside Container) Install Python 2 Compatibility Tools

```
sudo apt-get update
sudo apt-get install -y python-minimal python python-pip
```

---

## 7. Set Up SSH Key for GitHub

```
ssh-keygen -t rsa -b 4096 -C "contact@stonetusker.com"
```

Then add the `~/.ssh/id_rsa.pub` key to your GitHub [SSH Settings](https://github.com/settings/keys).

---

## 8. Clone Pre-patched Yocto BSP Repository

Use your forked repository that includes all necessary Yocto patches:

```
cd ~/work
git clone git@github.com:stonetusker/mys-8mmx-yocto.git
cd mys-8mmx-yocto
```

---

## 9. Pre-fetch Sources to Speed Up Build

```
export LANG=en_US.UTF-8
DISTRO=fsl-imx-xwayland MACHINE=mys-8mmx \
  source sources/meta-myir/tools/myir-setup-release.sh -b build-xwayland

bitbake myir-image-full --runall=fetch
```

 Note: Fetching sources can take ~1–3 hours depending on your hardware and internet speed.

---

## 10. Build the Yocto Image

```
bitbake myir-image-full
```

---

## 11. Locate Build Artifacts

Final images will be generated under:

```
build-xwayland/tmp/deploy/images/mys-8mmx/
```

### Example Files:
- `myir-image-full-mys-8mmx.sdcard`
- `u-boot.imx`
- `Image`
- `rootfs.tar.bz2`

---

## Summary

| Step             | Purpose                                              |
|------------------|------------------------------------------------------|
| Docker Setup     | Reproducible, clean Yocto build system               |
| SSH Key Setup    | Enables GitHub access in container                   |
| Repo Access      | Use pre-patched, clean Yocto BSP                     |
| Source Prefetch  | Boost performance by downloading all dependencies    |
| BitBake Build    | Compile production-quality embedded Linux images     |

---

### Notes

Yocto sources taken from https://d.myirtech.com/MYS-8MMX/L5.4.3/04-Source.tar.bz2 and updated to make it working.


---
Maintained by [stonetusker](mailto:contact@stonetusker.com)
```
