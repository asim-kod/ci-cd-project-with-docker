FROM ubuntu:22.04
USER root

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    curl git openssh-server sudo ca-certificates fuse3 fuse-overlayfs \
    && rm -rf /var/lib/apt/lists/*

RUN install -m 0755 -d /etc/apt/keyrings && \
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc && \
    chmod a+r /etc/apt/keyrings/docker.asc

RUN echo "Types: deb\n\
URIs: https://download.docker.com/linux/ubuntu\n\
Suites: jammy\n\
Components: stable\n\
Architectures: amd64\n\
Signed-By: /etc/apt/keyrings/docker.asc" > /etc/apt/sources.list.d/docker.sources

RUN apt-get update && apt-get install -y \
    docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin \
    && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /etc/docker && \
    echo '{"storage-driver": "fuse-overlayfs"}' > /etc/docker/daemon.json

RUN mkdir /var/run/sshd && \
    sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config

RUN curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" && \
    install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl && \
    rm kubectl

RUN curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.27.0/kind-linux-amd64 && \
    chmod +x ./kind && mv ./kind /usr/local/bin/kind

RUN useradd -m -s /bin/bash eksadmin && \
    echo "eksadmin:eksadmin" | chpasswd && \
    echo "eksadmin ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

RUN usermod -aG docker eksadmin

COPY --chown=eksadmin:eksadmin ./kind-config.yaml /home/eksadmin/kind-config.yaml

COPY --chown=eksadmin:eksadmin ./webapp_deployment /opt/webapp_deployment

CMD sh -c "dockerd > /var/log/dockerd.log 2>&1 & sleep 3 && /usr/sbin/sshd -D"