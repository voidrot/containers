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
        ssh \
    && rm -rf /var/lib/apt/lists/*

USER runner