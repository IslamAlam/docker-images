#
# Glances Dockerfile
#
# Copyright (c) Islam Mansour.
# Distributed under the terms of the Modified BSD License.

# Apline 3.11
# https://github.com/alpinelinux/docker-alpine/blob/c5510d5b1d2546d133f7b0938690c3c1e2cd9549/x86_64/Dockerfile
# OS/ARCH: linux/amd64, linux/386, linux/arm64, linux/ppc64le, linux/s390x, linux/arm/v7, linux/arm/v6
ARG ARG_WORKSPACE_VERSION="3.1.4"
ARG ARG_WORKSPACE_PATH="/glances"

ARG ROOT_CONTAINER=python:3.7-alpine3.11
ARG BASE_CONTAINER=$ROOT_CONTAINER
FROM $BASE_CONTAINER AS builder

ARG ARG_WORKSPACE_VERSION
ARG ARG_WORKSPACE_PATH
ARG ARG_BUILD_DATE
ENV GLANCES_VERSION=$ARG_WORKSPACE_VERSION
ENV GLANCES_PATH=$ARG_WORKSPACE_PATH

RUN apk add --no-cache --virtual .build_deps \
    bash \
	gcc \
    g++ \
	musl-dev \
	linux-headers \
    && export PIP_DEFAULT_NO_CACHE_DIR=true

RUN echo $GLANCES_PATH
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

ARG ARG_WORKSPACE_VERSION
ARG ARG_WORKSPACE_PATH
ARG ARG_BUILD_DATE="unknown"
ENV GLANCES_VERSION=$ARG_WORKSPACE_VERSION
ENV GLANCES_PATH=$ARG_WORKSPACE_PATH

# Copy our static executable.
COPY --from=builder /${GLANCES_PATH} /${GLANCES_PATH}
#COPY --from=builder /usr/local/lib/python3.7/site-packages/ /usr/local/lib/python3.7/site-packages/
COPY --from=builder /root/.local /root/.local
# Make sure scripts in .local are usable:
ENV PATH=/root/.local/bin:$PATH

RUN apk add --no-cache bash 

### END CONFIGURATION ### 
ENV WORKSPACE_VERSION=$ARG_WORKSPACE_VERSION

# Overwrite & add Labels
LABEL \
    "maintainer"="is3mansour@gmail.com" \
    "workspace.version"=$WORKSPACE_VERSION \
    "workspace.flavor"=$WORKSPACE_FLAVOR \
    # Kubernetes Labels
    "io.k8s.description"="Glances is a cross-platform system monitoring tool written in Python." \
    "io.k8s.display-name"="Glances - An Eye on your system" \
    # Openshift labels: https://docs.okd.io/latest/creating_images/metadata.html
    "io.openshift.expose-services"="61208:http, 61209:XMLRPC" \
    "io.openshift.non-scalable"="true" \
    "io.openshift.tags"="System, Monitoring, Glances" \
    "io.openshift.min-memory"="3Mb" \
    # Open Container labels: https://github.com/opencontainers/image-spec/blob/master/annotations.md
    "org.opencontainers.image.title"="Glances - An Eye on your system" \
    "org.opencontainers.image.description"="Glances is a cross-platform system monitoring tool written in Python." \
    "org.opencontainers.image.documentation"="https://nicolargo.github.io/glances/" \
    "org.opencontainers.image.url"="https://github.com/IslamAlam/docker-images" \
    "org.opencontainers.image.source"="https://github.com/IslamAlam/docker-images" \
    # "org.opencontainers.image.licenses"="Apache-2.0" \
    "org.opencontainers.image.version"=$WORKSPACE_VERSION \
    "org.opencontainers.image.vendor"="Islam Mansour" \
    "org.opencontainers.image.authors"="Islam Mansour" \
    "org.opencontainers.image.created"=$ARG_BUILD_DATE \ 
    # Label Schema Convention (deprecated): http://label-schema.org/rc1/
    "org.label-schema.name"="Glances - An Eye on your system" \
    "org.label-schema.description"="Glances is a cross-platform system monitoring tool written in Python." \
    "org.label-schema.usage"="https://nicolargo.github.io/glances/" \
    "org.label-schema.url"="https://github.com/IslamAlam/docker-images" \
    "org.label-schema.vcs-url"="https://github.com/IslamAlam/docker-images" \
    "org.label-schema.vendor"="Islam Mansour" \
    "org.label-schema.version"=$WORKSPACE_VERSION \
    "org.label-schema.schema-version"="1.0" \
    "org.label-schema.build-date"=$ARG_BUILD_DATE

# Define default command.
# CMD python -m glances -C /glances/conf/glances.conf --quiet $GLANCES_OPT

ENV GLANCES_PATH=$ARG_WORKSPACE_PATH
# Define working directory.
WORKDIR ${GLANCES_PATH}
# Configuration volume
VOLUME $GLANCES_PATH/conf

# Command line options
ENV GLANCES_OPT=-w

# Define default command.
CMD python -m glances -C /glances/conf/glances.conf $GLANCES_OPT


# Port 61208 is the Web UI port
# Port 61209 is the XMLRPC port
# See glances/conf/glances.conf for more ports

# EXPOSE PORT (For XMLRPC)
EXPOSE 61209

# EXPOSE PORT (For Web UI)
EXPOSE 61208
###
