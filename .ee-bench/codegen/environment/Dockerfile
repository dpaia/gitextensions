FROM mcr.microsoft.com/dotnet/sdk:9.0-noble
# Ubuntu 24.04

ENV DEBIAN_FRONTEND=noninteractive
ENV EnableWindowsTargeting=true

# Ubuntu server blocks a lot of requests, so we use a mirror (version for Ubuntu 23+)
RUN sed -i 's|http://archive.ubuntu.com/ubuntu/|http://eu-west-1.ec2.archive.ubuntu.com/ubuntu/|g' /etc/apt/sources.list.d/ubuntu.sources && \
    sed -i 's|http://security.ubuntu.com/ubuntu/|http://eu-west-1.ec2.archive.ubuntu.com/ubuntu/|g' /etc/apt/sources.list.d/ubuntu.sources && \
    apt-get update

# Install common packages
RUN apt-get update && \
    apt-get install -y \
    build-essential \
    git \
    curl \
    wget \
    sudo \
    tar \
    zip \
    unzip \
    autoconf \
    # Required for env
    libcurl4-gnutls-dev \
    libexpat1-dev \
    mono-complete \
    # Required for Junie
    libsecret-1-0 \
    libsecret-1-dev \
    rsync \
    python3 \
    python3-pip \
    && rm -rf /var/lib/apt/lists/*

RUN wget https://dot.net/v1/dotnet-install.sh -O dotnet-install.sh && \
    chmod +x ./dotnet-install.sh && \
    ./dotnet-install.sh --version 5.0.301 --install-dir /usr/share/dotnet && \
    ./dotnet-install.sh --version 10.0.100-preview.5.25277.114 --install-dir /usr/share/dotnet

# Install Windows Desktop runtime assemblies so tests referencing
# System.Windows.Forms can load on Linux.
RUN curl -sL -o /tmp/winrt.nupkg \
        https://api.nuget.org/v3-flatcontainer/microsoft.windowsdesktop.app.runtime.win-x64/9.0.0/microsoft.windowsdesktop.app.runtime.win-x64.9.0.0.nupkg && \
    mkdir -p /usr/share/dotnet/shared/Microsoft.WindowsDesktop.App/9.0.0 && \
    unzip -q /tmp/winrt.nupkg -d /tmp/winrt && \
    cp -r /tmp/winrt/runtimes/win-x64/lib/net9.0/* \
       /usr/share/dotnet/shared/Microsoft.WindowsDesktop.App/9.0.0/ && \
    rm -rf /tmp/winrt /tmp/winrt.nupkg

RUN git clone https://github.com/{{ instance.owner }}/{{ instance.repo_name }}.git /app
WORKDIR /app
RUN git checkout {{ instance.base_commit }}

LABEL ee-bench.type="codegen"
LABEL ee-bench.version="1.0"
RUN rm -rf /app/.ee-bench/ 2>/dev/null || true
