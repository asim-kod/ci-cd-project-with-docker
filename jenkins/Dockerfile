FROM jenkins/jenkins:lts
USER root

RUN apt-get update && apt-get install -y wget git

RUN mkdir -p /opt/maven && \
    wget https://dlcdn.apache.org/maven/maven-3/3.9.16/binaries/apache-maven-3.9.16-bin.tar.gz -P /opt/maven && \
    tar -xzf /opt/maven/apache-maven-3.9.16-bin.tar.gz -C /opt/maven --strip-components=1 && \
    rm /opt/maven/apache-maven-3.9.16-bin.tar.gz

ENV M2_HOME=/opt/maven
ENV MAVEN_HOME=/opt/maven
ENV PATH=$PATH:$M2_HOME/bin

USER jenkins