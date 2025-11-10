FROM registry.access.redhat.com/ubi9/ubi:latest

ARG ARTIFACTORY_URL
ARG ARTIFACTORY_USER
ARG ARTIFACTORY_API_KEY
ARG POSTGRES_RPM_PATH

RUN dnf install -y wget && \
    wget --user=${ARTIFACTORY_USER} --password=${ARTIFACTORY_API_KEY} \
    "${ARTIFACTORY_URL}/${POSTGRES_RPM_PATH}" -O /tmp/postgresql.rpm && \
    dnf localinstall -y /tmp/postgresql.rpm && \
    rm -f /tmp/postgresql.rpm && \
    dnf clean all

USER postgres