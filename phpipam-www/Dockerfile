#
# Glances Dockerfile
#
# Copyright (c) Islam Mansour.
# Distributed under the terms of the Modified BSD License.

# Apline 3.11
# https://github.com/alpinelinux/docker-alpine/blob/c5510d5b1d2546d133f7b0938690c3c1e2cd9549/x86_64/Dockerfile
# OS/ARCH: linux/amd64, linux/386, linux/arm64, linux/ppc64le, linux/s390x, linux/arm/v7, linux/arm/v6
ARG ARG_WORKSPACE_VERSION="1.4"
ARG ARG_WORKSPACE_PATH="/phpipam"

ARG ROOT_CONTAINER=alpine:3.11
ARG BASE_CONTAINER=$ROOT_CONTAINER
FROM $BASE_CONTAINER AS builder

ARG ARG_WORKSPACE_VERSION
ARG ARG_WORKSPACE_PATH
ARG ARG_BUILD_DATE
ENV PHPIPAM_VERSION=$ARG_WORKSPACE_VERSION
ENV PHPIPAM_PATH=$ARG_WORKSPACE_PATH

RUN apk upgrade --no-cache \
    && apk add --no-cache tini git tzdata \ 
    alpine-conf php php-cli php-pear php-pdo_mysql \ 
    php-json php-session php-gmp php-gd php-sockets \
    php-gettext php-mbstring php-ctype php-ldap \
    php-curl php-snmp php-openssl php-simplexml \
    php-pcntl php-iconv php-opcache php-posix iputils \
    fping apache2 php-apache2 \
    && sed -i 's/#LoadModule rewrite_module modules\/mod_rewrite.so/LoadModule rewrite_module modules\/mod_rewrite.so/' /etc/apache2/httpd.conf

ADD root/ /


ARG BUILD_DATE

ARG VCS_REF

ARG VERSION

ARG BRANCH
ENV BRANCH=$BRANCH


WORKDIR ${PHPIPAM_PATH}

# Download phpIPAM
RUN git clone -b ${PHPIPAM_VERSION} --recursive --depth 1 https://github.com/phpipam/phpipam.git ${PHPIPAM_PATH} \
    && cd ${PHPIPAM_PATH} \
    && git config core.fileMode false \
    && ln -s ${PHPIPAM_PATH}/config.docker.php ${PHPIPAM_PATH}/config.php \
    && chmod -R u=rw,go=r ${PHPIPAM_PATH} \
    && chmod -R a+X ${PHPIPAM_PATH} \
    && find ${PHPIPAM_PATH} -type d -name upload -exec chmod a=rwX {} \; \
    && chmod a=rwX ${PHPIPAM_PATH}/css/images/logo

RUN VCS_REF=`git rev-parse --short HEAD`

LABEL org.label-schema.build-date=$BUILD_DATE \
    org.label-schema.name="phpIPAM Apache Container" \
    org.label-schema.description="phpIPAM is an open-source web IP address management application (IPAM). Its goal is to provide light, modern and useful IP address management." \
    org.label-schema.url="https://phpipam.net/" \
    org.label-schema.vcs-ref=$VCS_REF \
    org.label-schema.vcs-url=https://github.com/phpipam/phpipam/ \
    org.label-schema.vendor=phpIPAM org.label-schema.version=$VERSION \
    org.label-schema.schema-version=1.0 maintainer="Islam Mansour <is3mansour@gmail.com>"

# Add tini
#ENV TINI_VERSION v0.19.0
#ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini-amd64 /tini
# https://github.com/krallin/tini/releases/download/v0.19.0/tini-amd64
#RUN chmod +x /tini

RUN apk add --no-cache tini
# Tini is now available at /sbin/tini
ENTRYPOINT ["/sbin/tini", "--"]

CMD /start_apache2

EXPOSE 80
# ENTRYPOINT ["/sbin/tini", "--"]
# ENTRYPOINT ["/tini", "-g", "--"]

# ADD start_apache2 /start_apache2
# ADD set_timezone /set_timezone
# ADD start_crond /start_crond
# ADD httpd.conf /etc/apache2/httpd.conf
# RUN chmod +x /start_apache2 /set_timezone /start_crond# 

# RUN echo "ServerName localhost" >> /etc/apache2/httpd.conf



# docker cp phpipam_phpipam-web_1:/etc/crontabs/ ./crontabs/

# docker build \
#         --build-arg VCS_REF=`git rev-parse --short HEAD` \
#         --build-arg BRANCH=`master` \
#         --build-arg BUILD_DATE=`date -u +"%Y-%m-%dT%H:%M:%SZ"` .

# RUN echo $GLANCES_PATH
# RUN mkdir -p ${GLANCES_PATH}
# 
# 
# # RUN apk --no-cache add ca-certificates tzdata
# RUN set -ex; \
# # 	apkArch="$(apk --print-arch)"; \
# # 	case "$apkArch" in \
# # 		armhf) arch='armv6' ;; \
# # 		aarch64) arch='arm64' ;; \
# # 		x86_64) arch='amd64' ;; \
# # 		*) echo >&2 "error: unsupported architecture: $apkArch"; exit 1 ;; \
# # 	esac; \
#  	wget --quiet -O /tmp/glances.tar.gz "https://github.com/nicolargo/glances/archive/v${GLANCES_VERSION}.tar.gz"; \
#  	tar xzvf /tmp/glances.tar.gz -C /${GLANCES_PATH} --strip-components=1; \
#  	rm -f /tmp/glances.tar.gz; \
#      # Install libs
#     # Glances issue #922: Do not install Sensors: PySensors
#     pip install --user -r /glances/requirements.txt \
#     && pip install --user -r /glances/optional-requirements.txt
# 
# ############################
# # STEP 2 build a small image
# ############################
# FROM $BASE_CONTAINER
# 
# ARG ARG_WORKSPACE_VERSION
# ARG ARG_WORKSPACE_PATH
# ARG ARG_BUILD_DATE="unknown"
# ENV GLANCES_VERSION=$ARG_WORKSPACE_VERSION
# ENV GLANCES_PATH=$ARG_WORKSPACE_PATH
# 
# # Copy our static executable.
# COPY --from=builder /${GLANCES_PATH} /${GLANCES_PATH}
# #COPY --from=builder /usr/local/lib/python3.7/site-packages/ /usr/local/lib/python3.7/site-packages/
# COPY --from=builder /root/.local /root/.local
# # Make sure scripts in .local are usable:
# ENV PATH=/root/.local/bin:$PATH
# 
# RUN apk add --no-cache bash 
# 
# ### END CONFIGURATION ### 
# ENV WORKSPACE_VERSION=$ARG_WORKSPACE_VERSION
# 
# # Overwrite & add Labels
# LABEL \
#     "maintainer"="is3mansour@gmail.com" \
#     "workspace.version"=$WORKSPACE_VERSION \
#     "workspace.flavor"=$WORKSPACE_FLAVOR \
#     # Kubernetes Labels
#     "io.k8s.description"="Glances is a cross-platform system monitoring tool written in Python." \
#     "io.k8s.display-name"="Glances - An Eye on your system" \
#     # Openshift labels: https://docs.okd.io/latest/creating_images/metadata.html
#     "io.openshift.expose-services"="61208:http, 61209:XMLRPC" \
#     "io.openshift.non-scalable"="true" \
#     "io.openshift.tags"="System, Monitoring, Glances" \
#     "io.openshift.min-memory"="3Mb" \
#     # Open Container labels: https://github.com/opencontainers/image-spec/blob/master/annotations.md
#     "org.opencontainers.image.title"="Glances - An Eye on your system" \
#     "org.opencontainers.image.description"="Glances is a cross-platform system monitoring tool written in Python." \
#     "org.opencontainers.image.documentation"="https://nicolargo.github.io/glances/" \
#     "org.opencontainers.image.url"="https://github.com/IslamAlam/docker-images" \
#     "org.opencontainers.image.source"="https://github.com/IslamAlam/docker-images" \
#     # "org.opencontainers.image.licenses"="Apache-2.0" \
#     "org.opencontainers.image.version"=$WORKSPACE_VERSION \
#     "org.opencontainers.image.vendor"="Islam Mansour" \
#     "org.opencontainers.image.authors"="Islam Mansour" \
#     "org.opencontainers.image.created"=$ARG_BUILD_DATE \ 
#     # Label Schema Convention (deprecated): http://label-schema.org/rc1/
#     "org.label-schema.name"="Glances - An Eye on your system" \
#     "org.label-schema.description"="Glances is a cross-platform system monitoring tool written in Python." \
#     "org.label-schema.usage"="https://nicolargo.github.io/glances/" \
#     "org.label-schema.url"="https://github.com/IslamAlam/docker-images" \
#     "org.label-schema.vcs-url"="https://github.com/IslamAlam/docker-images" \
#     "org.label-schema.vendor"="Islam Mansour" \
#     "org.label-schema.version"=$WORKSPACE_VERSION \
#     "org.label-schema.schema-version"="1.0" \
#     "org.label-schema.build-date"=$ARG_BUILD_DATE
# 
# # Define default command.
# # CMD python -m glances -C /glances/conf/glances.conf --quiet $GLANCES_OPT
# 
# ENV GLANCES_PATH=$ARG_WORKSPACE_PATH
# # Define working directory.
# WORKDIR ${GLANCES_PATH}
# # Configuration volume
# VOLUME $GLANCES_PATH/conf
# 
# # Command line options
# ENV GLANCES_OPT=-w
# 
# # Define default command.
# CMD python -m glances -C /glances/conf/glances.conf $GLANCES_OPT
# 
# 
# # Port 61208 is the Web UI port
# # Port 61209 is the XMLRPC port
# # See glances/conf/glances.conf for more ports
# 
# # EXPOSE PORT (For XMLRPC)
# EXPOSE 61209
# 
# # EXPOSE PORT (For Web UI)
# EXPOSE 61208
# ###
