# phpIPAM Cron container

ARG VERSION

# phpIPAM Apache container 
ARG ARG_WORKSPACE_VERSION="latest"
# Build from full flavor of workspace with same version
FROM imansour/phpipam-www:$ARG_WORKSPACE_VERSION

ARG ARG_WORKSPACE_FLAVOR="cron"
ENV WORKSPACE_FLAVOR=$ARG_WORKSPACE_FLAVOR
# argument needs to be initalized again
ARG ARG_WORKSPACE_VERSION="latest"
ENV WORKSPACE_VERSION=$ARG_WORKSPACE_VERSION


ARG BUILD_DATE
ARG VCS_REF
ARG VERSION

LABEL org.label-schema.build-date=$BUILD_DATE \
      org.label-schema.name="phpIPAM Cron Container" \
      org.label-schema.description="phpIPAM is an open-source web IP address management application (IPAM). Its goal is to provide light, modern and useful IP address management." \
      org.label-schema.url="https://phpipam.net/" \
      org.label-schema.vcs-ref=$VCS_REF \
      org.label-schema.vcs-url="https://github.com/phpipam/phpipam/" \
      org.label-schema.vendor="phpIPAM" \
      org.label-schema.version=$VERSION \
      org.label-schema.schema-version="1.0" \
      maintainer="Gary Allan <github@gallan.co.uk>"

WORKDIR /phpipam

# Run busybox crond
ENTRYPOINT ["/sbin/tini", "--"]
CMD /start_crond
