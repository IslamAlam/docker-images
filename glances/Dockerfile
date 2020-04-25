#
# Glances Dockerfile for ARM (based on Alpine ARM)
#
# https://github.com/nicolargo/glances
#
# Copyright (c) Islam Mansour.
# Distributed under the terms of the Modified BSD License.

# Apline 10.3 (bionic)
# https://github.com/alpinelinux/docker-alpine/blob/c5510d5b1d2546d133f7b0938690c3c1e2cd9549/x86_64/Dockerfile
# OS/ARCH: linux/amd64 
ARG ROOT_CONTAINER=alpine:3.11
ARG BASE_CONTAINER=$ROOT_CONTAINER
FROM $BASE_CONTAINER

LABEL maintainer="Islam Mansour <is3mansour@gmail.com>"
ARG NB_USER="jovyan"
ARG NB_UID="1000"
ARG NB_GID="100"


# Pull base image.
# FROM python:3.5-alpine

# Install Glances (develop branch)
#RUN apk add --no-cache --virtual .build_deps \
#	gcc \
#	musl-dev \
#	linux-headers \
#	&& pip install --no-cache-dir 'psutil>=5.4.7' bottle \
#	&& apk del .build_deps
RUN apk add --no-cache curl bash gcc && export PIP_DEFAULT_NO_CACHE_DIR=true && curl -L https://bit.ly/glances | /bin/bash

# Define working directory.
WORKDIR /glances

# EXPOSE PORT (For Web UI & XMLRPC)
EXPOSE 61208 61209

# Define default command.
CMD python -m glances -C /glances/conf/glances.conf --quiet $GLANCES_OPT