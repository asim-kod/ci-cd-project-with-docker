FROM ubuntu:22.04
USER root

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    ansible git openssh-server sudo curl ca-certificates \
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
    echo '{"storage-driver": "vfs"}' > /etc/docker/daemon.json

RUN mkdir /var/run/sshd && \
    sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config && \
    sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config

RUN useradd -m -s /bin/bash ansadmin && \
    echo "ansadmin:ansadmin" | chpasswd && \
    echo "ansadmin ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

RUN usermod -aG docker ansadmin

RUN mkdir -p /home/ansadmin/.ssh && \
    chown -R ansadmin:ansadmin /home/ansadmin/.ssh

COPY --chown=ansadmin:ansadmin ./hosts /etc/ansible/hosts

COPY --chown=ansadmin:ansadmin ./webapp_deployment /opt/webapp_deployment

CMD sh -c "dockerd > /var/log/dockerd.log 2>&1 & sleep 3 && /usr/sbin/sshd -D"