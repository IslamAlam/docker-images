#
# Glances Dockerfile
#
# https://github.com/nicolargo/glances
#
# Copyright (c) Islam Mansour.
# Distributed under the terms of the Modified BSD License.

# Apline 3.11
# https://github.com/alpinelinux/docker-alpine/blob/c5510d5b1d2546d133f7b0938690c3c1e2cd9549/x86_64/Dockerfile
# OS/ARCH: linux/amd64, linux/386, linux/arm64, linux/ppc64le, linux/s390x, linux/arm/v7, linux/arm/v6
ARG ROOT_CONTAINER=python:3.7-alpine3.11
ARG BASE_CONTAINER=$ROOT_CONTAINER
FROM $BASE_CONTAINER AS builder

LABEL maintainer="Islam Mansour <is3mansour@gmail.com>"


ENV GLANCES_VERSION=3.1.4.1
ENV GLANCES_PATH=/glances

RUN apk add --no-cache --virtual .build_deps \
    bash \
	gcc \
    g++ \
	musl-dev \
	linux-headers \
    && export PIP_DEFAULT_NO_CACHE_DIR=true

RUN mkdir -p ${GLANCES_PATH}


# RUN apk --no-cache add ca-certificates tzdata
RUN set -ex; \
# 	apkArch="$(apk --print-arch)"; \
# 	case "$apkArch" in \
# 		armhf) arch='armv6' ;; \
# 		aarch64) arch='arm64' ;; \
# 		x86_64) arch='amd64' ;; \
# 		*) echo >&2 "error: unsupported architecture: $apkArch"; exit 1 ;; \
# 	esac; \
 	wget --quiet -O /tmp/glances.tar.gz "https://github.com/nicolargo/glances/archive/v${GLANCES_VERSION}.tar.gz"; \
 	tar xzvf /tmp/glances.tar.gz -C /${GLANCES_PATH} --strip-components=1; \
 	rm -f /tmp/glances.tar.gz; \
     # Install libs
    # Glances issue #922: Do not install Sensors: PySensors
    pip install --user -r /glances/requirements.txt \
    && pip install --user -r /glances/optional-requirements.txt

############################
# STEP 2 build a small image
############################
FROM $BASE_CONTAINER

# Copy our static executable.
COPY --from=builder /glances /glances
#COPY --from=builder /usr/local/lib/python3.7/site-packages/ /usr/local/lib/python3.7/site-packages/
COPY --from=builder /root/.local /root/.local
# Make sure scripts in .local are usable:
ENV PATH=/root/.local/bin:$PATH

RUN apk add --no-cache bash 

# Define working directory.
WORKDIR /glances

# EXPOSE PORT (For Web UI & XMLRPC)
EXPOSE 61208 61209

# Define default command.
CMD python -m glances -C /glances/conf/glances.conf --quiet $GLANCES_OPT