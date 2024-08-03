FROM ghcr.io/actions/actions-runner:latest

LABEL org.opencontainers.image.source=https://github.com/voidrot/containers

USER root

RUN apt-get update \
    && apt-get upgrade -y -qq \
    && apt-get install -y -qq \
        curl \
        jq \
        rsync \
        unzip \
        wget \
        zip \
        openssh-client \
        ca-certificates \
    && rm -rf /var/lib/apt/lists/*

ADD https://minio.homelab.monster/public/certs/homelab-ca.pem /usr/local/share/ca-certificates/homelab-ca.crt
ADD https://minio.homelab.monster/public/certs/homelab-intermediate-ca.pem /usr/local/share/ca-certificates/homelab-intermediate-ca.crt

RUN chmod 644 /usr/local/share/ca-certificates/homelab-ca.crt \
    && chmod 644 /usr/local/share/ca-certificates/homelab-intermediate-ca.crt \
    && update-ca-certificates

RUN ln -sr /home/runner/externals/node20/bin/node /usr/local/bin/node \
    && ln -sr /home/runner/externals/node20/bin/npm /usr/local/bin/npm \
    && ln -sr /home/runner/externals/node20/bin/npx /usr/local/bin/npx

USER runner

ENV PATH="/home/runner/externals/node20/bin/:${PATH}"
ENV AGENT_TOOLSDIRECTORY=/opt/hostedtoolcache