FROM ubuntu:18.04

# Prevent prompt during package installs
ENV DEBIAN_FRONTEND=noninteractive

# Install system locales and basic utilities
RUN apt-get update && \
    apt-get install -y \
        locales \
        sudo \
        tzdata \
        wget \
        curl \
        git \
        vim \
        nano 

# Generate locale
RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    locale-gen

# Install core Yocto build tools and dependencies
RUN apt-get update && \
    apt-get install -y \
        gawk \
        wget \
        git-core \
        diffstat \
        unzip \
        zip \        
        texinfo \
        gcc \
        build-essential \
        chrpath \
        socat \
        cpio \
        python-minimal \
        python-pip \
        python \
        xz-utils \
        debianutils \
        iputils-ping \
        libsdl1.2-dev \
        xterm \
        zstd \
        psmisc

# Install additional helpful utilities
RUN apt-get update && \
    apt-get install -y \
        lzop \
        bc \
        rsync \
        u-boot-tools \
        liblz4-tool \
        screen \
        dos2unix \
        pigz \
        net-tools \
        bzip2 \
        file \
        libssl-dev \
        libtool \
        libtool-bin tree\
    && rm -rf /var/lib/apt/lists/*


# Add non-root user for builds
RUN groupadd -g 1000 build && \
    useradd -m -u 1000 -g build -s /bin/bash build && \
    echo "build ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# Switch to non-root build user
USER build
WORKDIR /home/build

# Configure git (optional)
RUN git config --global user.name "Stonetusker" && \
    git config --global user.email "contact@stonetusker.com"

# Set default shell to bash
SHELL ["/bin/bash", "-c"]

