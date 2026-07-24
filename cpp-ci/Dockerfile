# ---------- Base Image ----------
# Use Ubuntu 22.04 (Jammy) for modern glibc and good Conan binary compatibility
FROM ubuntu:jammy

# Prevent interactive prompts during package installation
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Etc/UTC

RUN sed -i 's@https://@http://@g' /etc/apt/sources.list && \
    apt-get update && \
    apt-get install -y --no-install-recommends ca-certificates && \
    rm -rf /var/lib/apt/lists/*

RUN sed -i 's@http://archive.ubuntu.com/ubuntu/@https://mirrors.tuna.tsinghua.edu.cn/ubuntu/@g' /etc/apt/sources.list && \
    sed -i 's@http://security.ubuntu.com/ubuntu/@https://mirrors.tuna.tsinghua.edu.cn/ubuntu/@g' /etc/apt/sources.list

# System Dependencies
RUN apt-get update && apt-get install -y \
    # system
    sudo \
    curl \
    wget \
    unzip \
    locales \
    jq \
    htop \
    iputils-ping \
    net-tools \
    vim \
    # Development Tools
    make \
    openssh-server \
    # Utilities
    git \
    # dev
    gcc \
    g++ \
    # Compiler toolchain (GCC, G++, Make)
    build-essential \
    autoconf \
    libtool \
    pkg-config \
    cmake \
    clang \
    libc++-dev \
    # Build systems
    ninja-build \
    # Debugging & SSH (Required for VS Code Remote)
    gdb \
    # Python (Required for Conan)
    python3 \
    python3-pip \
    python3-venv \
    && rm -rf /var/lib/apt/lists/*

# Locale Settings
# Set locale to UTF-8 to avoid encoding issues in logs and compilers
RUN locale-gen en_US.UTF-8
ENV LANG=en_US.UTF-8 \
    LANGUAGE=en_US:en \
    LC_ALL=en_US.UTF-8

# Install Conan (C/C++ Package Manager)
# Upgrade pip and install Conan 2.x
RUN pip3 install --upgrade pip && \
    pip3 install "conan>=2.0" && \
    pip3 install meson ninja

# Setup user
ARG USER_NAME=op1
ARG USER_UID=1001
ARG USER_GID=$USER_UID
ARG USER_HOME=/home/$USER_NAME
ARG USER_BIN=$USER_HOME/bin
ARG USER_PWD=pw${USER_NAME}111
ARG PATH=$USER_BIN:$PATH

RUN echo 'root:pwroot111' | chpasswd

# Create Non-Root User ----------
# Define arguments for user ID and group ID to match host machine

# Create group and user with sudo privileges
RUN groupadd --gid $USER_GID $USER_NAME \
    && useradd --uid $USER_UID --gid $USER_GID -m -s /bin/bash $USER_NAME \
    && echo $USER_NAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USER_NAME \
    && chmod 0440 /etc/sudoers.d/$USER_NAME

RUN echo "${USER_NAME}:${USER_PWD}" | chpasswd

RUN git config --global http.sslVerify false


# SSH Configuration for VS Code Remote-Containers ----------
# Create directory for sshd runtime files
RUN mkdir /run/sshd && \
    # Allow root login for provisioning (will run as non-root user later)
    echo "PermitRootLogin prohibit-password" >> /etc/ssh/sshd_config


# ---------- 6. Initialize Conan Profile ----------
# Switch to the non-root user to set up the Conan home directory
USER $USER_NAME
WORKDIR /home/$USER_NAME


RUN yes | ssh-keygen -f /home/$USER_NAME/.ssh/id_rsa && touch /home/$USER_NAME/.ssh/authorized_keys
#RUN mkdir -p bin project_mapping

# Detect the compiler and create a default Conan profile
RUN conan profile detect --force

# Container Startup ----------
# Switch back to root to run sshd
USER root

RUN echo "export PATH=$USER_BIN:$PATH" >> /etc/profile

# Set the working directory where the project will be mounted
WORKDIR /workspace

# Start the SSH daemon in the foreground
CMD ["/usr/sbin/sshd", "-D"]